<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref, watch } from "vue";
import {
  VisArea,
  VisAxis,
  VisGroupedBar,
  VisLine,
  VisXYContainer,
} from "@unovis/vue";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Slider } from "@/components/ui/slider";
import { Switch } from "@/components/ui/switch";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import {
  ChartContainer,
  ChartCrosshair,
  ChartTooltip,
  ChartTooltipContent,
  componentToString,
} from "@/components/ui/chart";
import { ChevronDown, ChevronUp } from "lucide-vue-next";

type DataPoint = {
  timestamp: number;
  clusterfuck: number;
  nightmareLatency: number;
  angryUsers: number;
};

type MetricKey = "clusterfuck" | "nightmareLatency" | "angryUsers";

const METRIC_ORDER: MetricKey[] = [
  "clusterfuck",
  "nightmareLatency",
  "angryUsers",
];

const CHART_COLORS: Record<MetricKey, string> = {
  clusterfuck: "#FF6B6B",
  nightmareLatency: "#4ECDC4",
  angryUsers: "#FFD166",
};

const chartConfig: Record<MetricKey, { label: string; color: string }> = {
  clusterfuck: {
    label: "Server Clusterfuck Index",
    color: CHART_COLORS.clusterfuck,
  },
  nightmareLatency: {
    label: "Response Time Nightmare",
    color: CHART_COLORS.nightmareLatency,
  },
  angryUsers: {
    label: "Pissed-off Users",
    color: CHART_COLORS.angryUsers,
  },
};

const data = ref<DataPoint[]>([]);
const isRunning = ref(true);
const updateInterval = ref(1000);
const volatility = ref(1);
const selectedMetrics = ref<Record<MetricKey, boolean>>({
  clusterfuck: true,
  nightmareLatency: true,
  angryUsers: true,
});
const visibleRows = ref(5);
const isExpanded = ref(false);
const isMobile = ref(false);

const updateIntervalModel = computed<number[]>({
  get: () => [updateInterval.value],
  set: (values) => {
    if (values?.[0] != null) updateInterval.value = values[0];
  },
});

const volatilityModel = computed<number[]>({
  get: () => [volatility.value],
  set: (values) => {
    if (values?.[0] != null) volatility.value = values[0];
  },
});

let timer: number | null = null;

const generateDataPoint = (timestamp: number, factor = 1): DataPoint => {
  const base = Math.sin(timestamp / 10) * 5 + 10;
  const random = Math.random() * 3 * factor;
  const value = Math.max(0, base + random);

  return {
    timestamp,
    clusterfuck: Math.round(value * 10) / 10,
    nightmareLatency: Math.max(
      0,
      Math.round((Math.sin(timestamp / 8) * 3 + Math.random() * 5) * 10) / 10
    ),
    angryUsers: Math.floor(Math.random() * 5) + Math.floor(value),
  };
};

const formatTimestamp = (timestamp: number) => {
  const date = new Date(timestamp);
  return `${date.getHours().toString().padStart(2, "0")}:${date
    .getMinutes()
    .toString()
    .padStart(2, "0")}:${date.getSeconds().toString().padStart(2, "0")}`;
};

const activeMetrics = computed<MetricKey[]>(() =>
  METRIC_ORDER.filter((key) => selectedMetrics.value[key])
);

const latest20 = computed(() => data.value.slice(-20));
const latest7 = computed(() => data.value.slice(-7));
const tableData = computed(() =>
  data.value.slice(-visibleRows.value).reverse()
);

const shouldShowContent = computed(() =>
  isMobile.value ? isExpanded.value : true
);

const gradientDefs = computed(() =>
  activeMetrics.value
    .map(
      (metric) => `
      <linearGradient id="gradient-${metric}" x1="0" y1="0" x2="0" y2="1">
        <stop offset="5%" stop-color="${chartConfig[metric].color}" stop-opacity="0.8" />
        <stop offset="95%" stop-color="${chartConfig[metric].color}" stop-opacity="0.1" />
      </linearGradient>
    `
    )
    .join("")
);

const xAccessor = (d: DataPoint) => d.timestamp;
const yAccessor = (metric: MetricKey) => (d: DataPoint) => d[metric];

const groupedBarYAccessors = computed(() =>
  activeMetrics.value.map((metric) => yAccessor(metric))
);

const groupedBarColors = computed(() =>
  activeMetrics.value.map((metric) => chartConfig[metric].color)
);

const updateViewport = () => {
  isMobile.value = window.innerWidth < 1024;
};

const addDataPoint = () => {
  const next = [...data.value, generateDataPoint(Date.now(), volatility.value)];
  data.value = next.length > 50 ? next.slice(next.length - 50) : next;
};

const seedData = () => {
  if (data.value.length > 0) return;
  const now = Date.now();
  const initial: DataPoint[] = [];
  for (let i = 20; i > 0; i -= 1) {
    initial.push(generateDataPoint(now - i * 1000, volatility.value));
  }
  data.value = initial;
};

const stopTimer = () => {
  if (timer !== null) {
    window.clearInterval(timer);
    timer = null;
  }
};

const startTimer = () => {
  stopTimer();
  if (isRunning.value) {
    timer = window.setInterval(addDataPoint, updateInterval.value);
  }
};

watch([isRunning, updateInterval], startTimer);

watch(volatility, () => {
  if (data.value.length > 0) addDataPoint();
});

onMounted(() => {
  updateViewport();
  window.addEventListener("resize", updateViewport);
  seedData();
  startTimer();
});

onBeforeUnmount(() => {
  stopTimer();
  window.removeEventListener("resize", updateViewport);
});
</script>

<template>
  <div class="rounded-lg border p-2 bg-background text-foreground vue-chart-demo">
    <button
      v-if="isMobile"
      type="button"
      class="w-full flex items-center justify-between px-2 py-3 text-left font-medium"
      @click="isExpanded = !isExpanded"
    >
      <span class="flex items-center gap-2 truncate">
        <span
          class="bg-amber-100 dark:bg-amber-800 text-amber-800 dark:text-amber-100 px-2 py-0.5 rounded-md text-xs font-semibold whitespace-nowrap"
        >
          SHOW THE MAGIC
        </span>
        <span class="truncate whitespace-nowrap">Vue State Handling Demo</span>
      </span>
      <div class="text-lg">
        <ChevronUp v-if="isExpanded" class="h-5 w-5" />
        <ChevronDown v-else class="h-5 w-5" />
      </div>
    </button>

    <div v-if="shouldShowContent" :class="isMobile ? 'pt-2' : ''">
      <div class="flex flex-col lg:flex-row gap-2">
        <!-- Controls -->
        <div class="w-full lg:w-1/4 space-y-4 p-3 border rounded-md bg-background/50">
          <div class="flex items-center justify-between">
            <Label for="running-toggle" class="font-medium">
              Shit Show Status
            </Label>
            <div class="flex items-center gap-2">
              <Switch id="running-toggle" v-model="isRunning" />
              <span class="text-xs">
                {{ isRunning ? "Generating Chaos" : "Blissful Pause" }}
              </span>
            </div>
          </div>

          <div class="space-y-2">
            <Label for="interval-slider" class="font-medium">
              Clusterfuck Speed
            </Label>
            <Slider
              id="interval-slider"
              v-model="updateIntervalModel"
              :min="200"
              :max="2000"
              :step="100"
            />
            <div class="text-xs text-right">{{ updateInterval }}ms</div>
          </div>

          <div class="space-y-2">
            <Label for="volatility-slider" class="font-medium">
              Chaos Multiplier
            </Label>
            <Slider
              id="volatility-slider"
              v-model="volatilityModel"
              :min="0.5"
              :max="3"
              :step="0.1"
            />
            <div class="text-xs text-right">
              {{ volatility.toFixed(1) }}x
            </div>
          </div>

          <div class="space-y-2">
            <Label class="font-medium">
              Metrics to Give a Fuck About
            </Label>
            <div class="grid gap-1.5">
              <div
                v-for="key in METRIC_ORDER"
                :key="key"
                class="flex items-center gap-2"
              >
                <Switch :id="`metric-${key}`" v-model="selectedMetrics[key]" />
                <Label :for="`metric-${key}`" class="text-xs">
                  {{ chartConfig[key].label }}
                </Label>
              </div>
            </div>
          </div>

          <div class="space-y-2">
            <Label for="rows-visible" class="font-medium">
              Data Rows to Show
            </Label>
            <Input
              id="rows-visible"
              v-model.number="visibleRows"
              type="number"
              min="1"
              max="20"
              class="h-8"
              @change="
                visibleRows = Math.max(1, Math.min(20, Number(visibleRows) || 5))
              "
            />
          </div>

          <div
            class="text-xs text-muted-foreground p-2 border rounded-md bg-amber-50 dark:bg-amber-950/20 border-amber-200 dark:border-amber-800"
          >
            <strong>Try this shit yourself:</strong>
            Toggle metrics, adjust speed, inject chaos — this isn&apos;t even
            fucking possible with plain HTML. You&apos;d need a goddamn framework.
            Vanilla JS would be a fucking nightmare here, a recursive hellscape
            of DOM manipulation and callback bullshit. Vue handles this
            complexity so you don&apos;t lose your goddamn mind. State
            management isn&apos;t a luxury, it&apos;s a fucking necessity.
          </div>
        </div>

        <div class="w-full lg:w-3/4 space-y-3 sm:space-y-2">
          <!-- Area chart -->
          <div class="border rounded-md p-3 pb-0 h-88 sm:h-72 w-full bg-background/50">
            <div class="text-xs font-medium mb-2 text-muted-foreground">
              Real-time Shitstorm Dashboard (Last 20 Data Points)
            </div>

            <ChartContainer
              :config="chartConfig"
              class="h-[calc(100%-24px)] w-full flex flex-col overflow-hidden"
            >
              <div class="shrink-0 my-3 flex flex-wrap items-center justify-center gap-x-4 gap-y-1 text-[10px] sm:text-xs">
                <div
                  v-for="metric in activeMetrics"
                  :key="`legend-area-${metric}`"
                  class="flex items-center gap-1.5"
                >
                  <span
                    class="inline-block h-2.5 w-2.5 rounded-[2px]"
                    :style="{ backgroundColor: chartConfig[metric].color }"
                  />
                  <span class="leading-none">
                    {{ chartConfig[metric].label }}
                  </span>
                </div>
              </div>

              <div class="flex-1 min-h-0">
                <VisXYContainer
                  :data="latest20"
                  :svgDefs="gradientDefs"
                  :margin="
                    isMobile
                      ? { top: 6, right: 8, bottom: 2, left: 2 }
                      : { top: 8, right: 12, bottom: 4, left: 4 }
                  "
                >
                  <VisAxis
                    type="x"
                    :x="xAccessor"
                    :gridLine="true"
                    :tickFormat="(v: number) => formatTimestamp(v).split(':')[2]"
                    :tickLine="false"
                    :domainLine="false"
                  />
                  <VisAxis
                    type="y"
                    :gridLine="true"
                    :tickLine="false"
                    :domainLine="false"
                  />

                  <template v-for="metric in activeMetrics" :key="`area-${metric}`">
                    <VisArea
                      :x="xAccessor"
                      :y="yAccessor(metric)"
                      :color="`url(#gradient-${metric})`"
                      :opacity="0.6"
                    />
                    <VisLine
                      :x="xAccessor"
                      :y="yAccessor(metric)"
                      :color="chartConfig[metric].color"
                      :lineWidth="2"
                    />
                  </template>

                  <ChartTooltip />
                  <ChartCrosshair
                    :template="
                      componentToString(chartConfig, ChartTooltipContent, {
                        labelFormatter: (v: number | Date) =>
                          formatTimestamp(Number(v)),
                      })
                    "
                    :color="
                      (_d: DataPoint, i: number) =>
                        chartConfig[
                          activeMetrics[i % activeMetrics.length] || 'clusterfuck'
                        ].color
                    "
                  />
                </VisXYContainer>
              </div>
            </ChartContainer>
          </div>

          <!-- Bar chart -->
          <div class="border rounded-md p-3 pb-0 h-88 sm:h-72 w-full bg-background/50">
            <div class="text-xs font-medium mb-2 text-muted-foreground">
              Recent Dumpster Fire Metrics (Last 7 Data Points)
            </div>

            <ChartContainer
              :config="chartConfig"
              class="h-[calc(100%-24px)] w-full flex flex-col overflow-hidden"
            >
              <div class="shrink-0 my-3 flex flex-wrap items-center justify-center gap-x-4 gap-y-1 text-[10px] sm:text-xs">
                <div
                  v-for="metric in activeMetrics"
                  :key="`legend-bar-${metric}`"
                  class="flex items-center gap-1.5"
                >
                  <span
                    class="inline-block h-2.5 w-2.5 rounded-[2px]"
                    :style="{ backgroundColor: chartConfig[metric].color }"
                  />
                  <span class="leading-none">
                    {{ chartConfig[metric].label }}
                  </span>
                </div>
              </div>

              <div class="flex-1 min-h-0">
                <VisXYContainer
                  :data="latest7"
                  :padding="
                    isMobile
                      ? { top: 6, right: 8, bottom: 2, left: 2 }
                      : { top: 8, right: 12, bottom: 4, left: 4 }
                  "
                >
                  <VisAxis
                    type="x"
                    :x="xAccessor"
                    :gridLine="true"
                    :tickFormat="(v: number) => formatTimestamp(v).split(':')[2]"
                    :tickLine="false"
                    :domainLine="false"
                  />
                  <VisAxis
                    type="y"
                    :gridLine="true"
                    :tickLine="false"
                    :domainLine="false"
                  />

                  <VisGroupedBar
                    :x="xAccessor"
                    :y="groupedBarYAccessors"
                    :color="groupedBarColors"
                    :rounded-corners="4"
                    bar-padding="0.15"
                    group-padding="0"
                  />

                  <ChartTooltip />
                  <ChartCrosshair
                    :template="
                      componentToString(chartConfig, ChartTooltipContent, {
                        indicator: 'dashed',
                        hideLabel: true,
                      })
                    "
                    color="#0000"
                  />
                </VisXYContainer>
              </div>
            </ChartContainer>
          </div>

          <!-- Table -->
          <div class="border rounded-md p-3 w-full bg-background/50">
            <Table class="mt-0 mb-0">
              <TableHeader class="text-xs">
                <TableRow>
                  <TableHead class="w-[100px]">Timestamp</TableHead>
                  <TableHead v-for="metric in activeMetrics" :key="metric">
                    {{ chartConfig[metric].label }}
                  </TableHead>
                </TableRow>
              </TableHeader>

              <TableBody class="text-sm">
                <TableRow v-if="tableData.length === 0">
                  <TableCell
                    :colspan="activeMetrics.length + 1"
                    class="h-12 text-center text-muted-foreground"
                  >
                    No data yet. Hold your damn horses.
                  </TableCell>
                </TableRow>

                <TableRow
                  v-for="point in tableData"
                  :key="point.timestamp"
                  class="py-16"
                >
                  <TableCell class="font-mono text-xs">
                    {{ formatTimestamp(point.timestamp) }}
                  </TableCell>
                  <TableCell
                    v-for="metric in activeMetrics"
                    :key="`${point.timestamp}-${metric}`"
                    class="tabular-nums"
                  >
                    {{ point[metric] }}{{ metric === "angryUsers" ? " angry souls" : "" }}
                  </TableCell>
                </TableRow>
              </TableBody>
            </Table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.vue-chart-demo :deep(.vis-axis-grid-line) {
  stroke: var(--border);
  opacity: 0.35;
  stroke-dasharray: 3 3;
}

.vue-chart-demo :deep(.vis-axis-tick text) {
  font-size: 11px;
  fill: hsl(var(--muted-foreground));
}

.vue-chart-demo :deep(.vis-axis-label) {
  fill: hsl(var(--muted-foreground));
}

.vue-chart-demo :deep(.vis-area) {
  pointer-events: none;
}
</style>