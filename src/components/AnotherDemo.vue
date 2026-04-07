<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref, watch } from "vue";
import { CheckIcon, ChevronsUpDownIcon } from "lucide-vue-next";
import { cn } from "@/lib/utils";
import { Button } from "@/components/ui/button";
import { Checkbox } from "@/components/ui/checkbox";
import {
  Command,
  CommandEmpty,
  CommandGroup,
  CommandInput,
  CommandItem,
  CommandList,
} from "@/components/ui/command";
import { Input } from "@/components/ui/input";
import { Slider } from "@/components/ui/slider";
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from "@/components/ui/popover";

type Stage = "backlog" | "building" | "qa" | "shipped";

type Card = {
  id: number;
  title: string;
  points: number;
  stage: Stage;
  risk: "low" | "medium" | "high";
};

type Mutation = {
  id: number;
  cardId: number;
  toStage: Stage;
  status: "pending" | "ok" | "rolled_back";
};

const STAGES: Stage[] = ["backlog", "building", "qa", "shipped"];
const STORAGE_KEY = "vue-impossible-demo-v1";

const cards = ref<Card[]>([
  {
    id: 1,
    title: "Realtime dashboard with role-based widgets",
    points: 8,
    stage: "building",
    risk: "high",
  },
  {
    id: 2,
    title: "Offline draft editor + conflict merge",
    points: 5,
    stage: "qa",
    risk: "medium",
  },
  {
    id: 3,
    title: "Chunk-level cache invalidation strategy",
    points: 13,
    stage: "backlog",
    risk: "high",
  },
  {
    id: 4,
    title: "Multi-step payment flow with recovery",
    points: 8,
    stage: "building",
    risk: "medium",
  },
]);

const nextId = ref(5);
const draft = ref("");
const selectedCardId = ref<number | null>(cards.value[0]?.id ?? null);
const targetStage = ref<Stage>("qa");
const isAutoPilot = ref(false);
const cardPickerOpen = ref(false);
const stagePickerOpen = ref(false);
const autoPilotDelay = ref(1200);

const history = ref<Card[][]>([]);
const future = ref<Card[][]>([]);
const pendingMutations = ref<Mutation[]>([]);
const draggedCardId = ref<number | null>(null);
const dragOverStage = ref<Stage | null>(null);
const autopilotMoves = ref(0);
let mutationId = 1;
let autoPilotTimer: number | null = null;

const cloneCards = (value: Card[]) => value.map((card) => ({ ...card }));

const pushHistory = () => {
  history.value.push(cloneCards(cards.value));
  if (history.value.length > 30) history.value.shift();
  future.value = [];
};

const stageBuckets = computed(() =>
  STAGES.map((stage) => ({
    stage,
    cards: cards.value.filter((card) => card.stage === stage),
  })),
);

const totalPoints = computed(() =>
  cards.value.reduce((sum, card) => sum + card.points, 0),
);

const riskScore = computed(() =>
  cards.value.reduce((sum, card) => {
    const weight = card.risk === "high" ? 3 : card.risk === "medium" ? 2 : 1;
    return sum + card.points * weight;
  }, 0),
);

const flowHealth = computed(() => {
  const shipped = cards.value.filter((card) => card.stage === "shipped").length;
  const blocked = cards.value.filter(
    (card) => card.risk === "high" && card.stage !== "shipped",
  ).length;
  return Math.max(0, 100 - blocked * 12 + shipped * 8);
});

const selectedCard = computed(
  () => cards.value.find((card) => card.id === selectedCardId.value) ?? null,
);

const selectedCardLabel = computed(() => {
  if (!selectedCard.value) return "Select card...";
  return `#${selectedCard.value.id} - ${selectedCard.value.title}`;
});

const targetStageLabel = computed(() => `Move to ${targetStage.value}`);
const autoPilotDelayModel = computed<number[]>({
  get: () => [autoPilotDelay.value],
  set: (values) => {
    if (values?.[0] != null) autoPilotDelay.value = values[0];
  },
});

const selectCard = (cardId: number) => {
  selectedCardId.value = cardId;
  cardPickerOpen.value = false;
};

const selectStage = (stage: Stage) => {
  targetStage.value = stage;
  stagePickerOpen.value = false;
};

const addCard = () => {
  const title = draft.value.trim();
  if (!title) return;
  pushHistory();
  cards.value.unshift({
    id: nextId.value++,
    title,
    points: [3, 5, 8, 13][Math.floor(Math.random() * 4)],
    stage: "backlog",
    risk: ["low", "medium", "high"][
      Math.floor(Math.random() * 3)
    ] as Card["risk"],
  });
  draft.value = "";
};

const undo = () => {
  const prev = history.value.pop();
  if (!prev) return;
  future.value.push(cloneCards(cards.value));
  cards.value = cloneCards(prev);
};

const redo = () => {
  const next = future.value.pop();
  if (!next) return;
  history.value.push(cloneCards(cards.value));
  cards.value = cloneCards(next);
};

const applyOptimisticMove = (cardId: number, nextStage: Stage) => {
  const current = cards.value.find((card) => card.id === cardId);
  if (!current || current.stage === nextStage) return;
  pushHistory();
  const mutation: Mutation = {
    id: mutationId++,
    cardId,
    toStage: nextStage,
    status: "pending",
  };
  pendingMutations.value.unshift(mutation);

  const prevStage = current.stage;
  current.stage = mutation.toStage;

  const latency = 300 + Math.floor(Math.random() * 1200);
  const failed = Math.random() < 0.25;

  window.setTimeout(() => {
    const row = pendingMutations.value.find((item) => item.id === mutation.id);
    if (!row) return;

    const currentCard = cards.value.find((card) => card.id === mutation.cardId);
    if (failed && currentCard) {
      currentCard.stage = prevStage;
      row.status = "rolled_back";
      return;
    }
    row.status = "ok";
  }, latency);
};

const moveCardOptimistically = () => {
  if (!selectedCard.value) return;
  applyOptimisticMove(selectedCard.value.id, targetStage.value);
};

const onDragStart = (cardId: number) => {
  draggedCardId.value = cardId;
};

const onDropStage = (stage: Stage) => {
  if (draggedCardId.value == null) return;
  selectedCardId.value = draggedCardId.value;
  targetStage.value = stage;
  applyOptimisticMove(draggedCardId.value, stage);
  draggedCardId.value = null;
  dragOverStage.value = null;
};

const onDragEnd = () => {
  draggedCardId.value = null;
  dragOverStage.value = null;
};

const randomMove = () => {
  if (cards.value.length === 0) return;
  const randomCard =
    cards.value[Math.floor(Math.random() * cards.value.length)];
  const possibleStages = STAGES.filter((stage) => stage !== randomCard.stage);
  selectedCardId.value = randomCard.id;
  targetStage.value =
    possibleStages[Math.floor(Math.random() * possibleStages.length)];
  applyOptimisticMove(randomCard.id, targetStage.value);
  autopilotMoves.value += 1;
};

const startAutoPilot = () => {
  if (autoPilotTimer !== null) return;
  // Fire one move immediately so users can see it is alive.
  randomMove();
  autoPilotTimer = window.setInterval(randomMove, autoPilotDelay.value);
};

const stopAutoPilot = () => {
  if (autoPilotTimer === null) return;
  window.clearInterval(autoPilotTimer);
  autoPilotTimer = null;
};

watch(isAutoPilot, (enabled) => {
  if (enabled) startAutoPilot();
  else stopAutoPilot();
});

watch(autoPilotDelay, () => {
  if (!isAutoPilot.value) return;
  stopAutoPilot();
  startAutoPilot();
});

watch(
  cards,
  (value) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(value));
  },
  { deep: true },
);

onMounted(() => {
  try {
    const saved = localStorage.getItem(STORAGE_KEY);
    if (!saved) return;
    const parsed = JSON.parse(saved) as Card[];
    if (Array.isArray(parsed) && parsed.length > 0) {
      cards.value = parsed;
      nextId.value = Math.max(...parsed.map((card) => card.id)) + 1;
      selectedCardId.value = parsed[0]?.id ?? null;
    }
  } catch {
    // ignore corrupted localStorage state
  }
});

onBeforeUnmount(() => {
  stopAutoPilot();
});
</script>

<template>
  <section
    class="mb-5 rounded-lg border p-4 bg-background text-foreground space-y-4"
  >
    <p class="mt-0 text-sm">
      This is where Vue starts clowning on ad-hoc vanilla code: optimistic
      updates, rollback on failure, time-travel undo/redo, derived analytics,
      and local persistence all synced from one reactive state tree.
    </p>

    <div class="grid gap-2 sm:grid-cols-3 lg:grid-cols-4 text-sm">
      <div class="rounded-md border p-3">
        <div class="text-muted-foreground text-xs">Total Story Points</div>
        <div class="text-xl font-semibold">{{ totalPoints }}</div>
      </div>
      <div class="rounded-md border p-3">
        <div class="text-muted-foreground text-xs">Risk Score</div>
        <div class="text-xl font-semibold">{{ riskScore }}</div>
      </div>
      <div class="rounded-md border p-3">
        <div class="text-muted-foreground text-xs">Flow Health</div>
        <div class="text-xl font-semibold">{{ flowHealth }}%</div>
      </div>
      <div class="rounded-md border p-3">
        <div class="text-muted-foreground text-xs">Pending Mutations</div>
        <div class="text-xl font-semibold">
          {{ pendingMutations.filter((m) => m.status === "pending").length }}
        </div>
      </div>
    </div>

    <div class="flex flex-col sm:flex-row gap-2">
      <Input
        v-model="draft"
        placeholder="Add nasty enterprise feature..."
        @keydown.enter="addCard"
      />
      <Button @click="addCard">Add Card</Button>
      <Button variant="outline" :disabled="history.length === 0" @click="undo"
        >Undo</Button
      >
      <Button variant="outline" :disabled="future.length === 0" @click="redo"
        >Redo</Button
      >
    </div>

    <div class="w-full flex flex-col lg:flex-row gap-2">
      <Popover v-model:open="cardPickerOpen">
        <PopoverTrigger as-child>
          <Button
            variant="outline"
            role="combobox"
            :aria-expanded="cardPickerOpen"
            class="w-full lg:w-[320px] max-w-full justify-between"
          >
            <span class="truncate text-left">{{ selectedCardLabel }}</span>
            <ChevronsUpDownIcon class="ml-2 h-4 w-4 opacity-50" />
          </Button>
        </PopoverTrigger>
        <PopoverContent class="w-[320px] max-w-[calc(100vw-2rem)] p-0">
          <Command>
            <CommandInput class="h-9" placeholder="Search card..." />
            <CommandList>
              <CommandEmpty>No card found.</CommandEmpty>
              <CommandGroup>
                <CommandItem
                  v-for="card in cards"
                  :key="card.id"
                  :value="`${card.id} ${card.title}`"
                  @select="() => selectCard(card.id)"
                >
                  <span class="truncate">#{{ card.id }} - {{ card.title }}</span>
                  <CheckIcon
                    :class="
                      cn(
                        'ml-auto h-4 w-4',
                        selectedCardId === card.id
                          ? 'opacity-100'
                          : 'opacity-0',
                      )
                    "
                  />
                </CommandItem>
              </CommandGroup>
            </CommandList>
          </Command>
        </PopoverContent>
      </Popover>

      <Popover v-model:open="stagePickerOpen">
        <PopoverTrigger as-child>
          <Button
            variant="outline"
            role="combobox"
            :aria-expanded="stagePickerOpen"
            class="w-full lg:w-[190px] justify-between"
          >
            {{ targetStageLabel }}
            <ChevronsUpDownIcon class="ml-2 h-4 w-4 opacity-50" />
          </Button>
        </PopoverTrigger>
        <PopoverContent class="min-w-[220px] p-0">
          <Command>
            <CommandInput class="h-9" placeholder="Search stage..." />
            <CommandList>
              <CommandEmpty>No stage found.</CommandEmpty>
              <CommandGroup>
                <CommandItem
                  v-for="stage in STAGES"
                  :key="stage"
                  :value="stage"
                  @select="() => selectStage(stage)"
                >
                  Move to {{ stage }}
                  <CheckIcon
                    :class="
                      cn(
                        'ml-auto h-4 w-4',
                        targetStage === stage ? 'opacity-100' : 'opacity-0',
                      )
                    "
                  />
                </CommandItem>
              </CommandGroup>
            </CommandList>
          </Command>
        </PopoverContent>
      </Popover>

      <Button class="w-full lg:w-auto" @click="moveCardOptimistically"
        >Optimistic Move + Server Sim</Button
      >
    </div>
    <div class="flex flex-col sm:flex-row sm:items-center gap-2 sm:gap-3">
      <label class="flex items-center gap-2 text-sm">
        <Checkbox v-model="isAutoPilot" />
        chaos autopilot
      </label>
      <div class="text-xs text-muted-foreground">
        speed: {{ autoPilotDelay }}ms
      </div>
      <div class="w-full sm:w-[220px]">
        <Slider
          v-model="autoPilotDelayModel"
          :min="10"
          :max="2500"
          :step="10"
        />
      </div>
      <span v-if="isAutoPilot" class="text-xs">
        (autopilot active - moves: {{ autopilotMoves }})
      </span>
    </div>

    <div class="grid gap-2 lg:grid-cols-4">
      <div
        v-for="bucket in stageBuckets"
        :key="bucket.stage"
        :class="
          cn(
            'rounded-md border p-2 space-y-2 min-h-[160px] transition-colors',
            dragOverStage === bucket.stage && 'border-primary bg-primary/5',
          )
        "
        @dragover.prevent="dragOverStage = bucket.stage"
        @dragleave="dragOverStage = null"
        @drop.prevent="onDropStage(bucket.stage)"
      >
        <div class="text-xs uppercase tracking-wide text-muted-foreground">
          {{ bucket.stage }} ({{ bucket.cards.length }})
        </div>
        <div
          v-for="card in bucket.cards"
          :key="card.id"
          class="rounded border p-2 cursor-grab active:cursor-grabbing"
          draggable="true"
          @dragstart="onDragStart(card.id)"
          @dragend="onDragEnd"
        >
          <div class="text-sm font-medium">#{{ card.id }} {{ card.title }}</div>
          <div class="text-xs text-muted-foreground">
            {{ card.points }} pts - risk: {{ card.risk }}
          </div>
        </div>
      </div>
    </div>

    <div class="rounded-md border p-2 text-xs space-y-1">
      <div class="font-medium">Mutation Log (newest first)</div>
      <div
        v-for="mutation in pendingMutations.slice(0, 6)"
        :key="mutation.id"
        class="font-mono"
      >
        #{{ mutation.id }} card {{ mutation.cardId }} ->
        {{ mutation.toStage }} :
        <span
          :class="[
            mutation.status === 'ok' && 'text-emerald-600',
            mutation.status === 'pending' && 'text-amber-600',
            mutation.status === 'rolled_back' && 'text-red-600',
          ]"
        >
          {{ mutation.status }}
        </span>
      </div>
    </div>
  </section>
</template>
