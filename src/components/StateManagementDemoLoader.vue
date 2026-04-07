<script setup lang="ts">
import { defineAsyncComponent, onBeforeUnmount, onMounted, ref } from "vue";
import { cn } from "@/lib/utils";

const StateManagementDemo = defineAsyncComponent(
  () => import("./StateManagementDemo.vue"),
);

const markerRef = ref<HTMLDivElement | null>(null);
const shouldLoad = ref(false);
let observer: IntersectionObserver | null = null;

onMounted(() => {
  observer = new IntersectionObserver(
    (entries) => {
      if (entries.some((entry) => entry.isIntersecting)) {
        shouldLoad.value = true;
        observer?.disconnect();
      }
    },
    { threshold: 0.1 },
  );

  if (markerRef.value) {
    observer.observe(markerRef.value);
  }
});

onBeforeUnmount(() => {
  observer?.disconnect();
});
</script>

<template>
  <div
    ref="markerRef"
    :class="
      cn(
        'rounded-lg border p-2 bg-background text-foreground h-[50vh]',
        shouldLoad && 'hidden',
      )
    "
  />

  <div
    :class="
      cn(
        'opacity-0',
        shouldLoad && 'opacity-100 transition-opacity duration-500',
      )
    "
  >
    <StateManagementDemo v-if="shouldLoad" />
  </div>
</template>
