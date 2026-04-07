<script setup lang="ts">
import { computed, onMounted, ref } from "vue";
import { Moon, Sun } from "lucide-vue-next";
import { Button } from "@/components/ui/button";

const theme = ref<"light" | "dark">("light");

const isDark = computed(() => theme.value === "dark");

const applyTheme = (next: "light" | "dark") => {
  theme.value = next;
  document.documentElement.classList.toggle("dark", next === "dark");
  localStorage.setItem("theme", next);
};

const toggleTheme = () => {
  applyTheme(isDark.value ? "light" : "dark");
};

onMounted(() => {
  const saved = localStorage.getItem("theme") as "light" | "dark" | null;
  const prefersDark = window.matchMedia("(prefers-color-scheme: dark)").matches;
  applyTheme(saved ?? (prefersDark ? "dark" : "light"));
});
</script>

<template>
  <Button class="cursor-pointer" size="icon" type="button" @click="toggleTheme">
    <Sun class="h-6 w-5 dark:hidden" />
    <Moon class="hidden h-5 w-5 dark:block" />
    <span class="sr-only">Toggle theme</span>
  </Button>
</template>
