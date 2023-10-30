<template>
  <div class="mt-[28px] h-full w-full flex justify-center overflow-hidden">
    <Dock class="fixed bottom-0 z-[999]" />
    <div class="w-full h-full pt-[28px]">
      <Window
        v-for="window in windows"
        :key="window.id"
        :window-data="window"
        v-show="isWindowVisible"
        :style="{ zIndex: window.zIndex }"
        @pointerdown="bringToFront(window)"
        :title="'test app'"
      >
        <p class="mt-2">{{ window.content }}</p>
      </Window>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, provide } from "vue";
import windowsData from "@/assets/apps.json";

export default {
  setup() {
    const isWindowVisible = ref(true);
    const windows = reactive(windowsData);

    const bringToFront = (clickedWindow) => {
      const maxZIndex = windows.length;
      clickedWindow.zIndex = maxZIndex;

      windows
        .filter((window) => window !== clickedWindow)
        .sort((a, b) => b.zIndex - a.zIndex)
        .forEach((window, index) => {
          window.zIndex = maxZIndex - index - 1;
        });
    };
    const toggleWindowVisibility = (id) => {
      const windowToToggle = windows.find((w) => w.id === id);
      if (windowToToggle) {
        windowToToggle.isVisible = !windowToToggle.isVisible;
      }
    };
    const openWindow = (id) => {
      const windowToOpen = windows.find((w) => w.id === id);
      windowToOpen.isOpen = true;
      if (windowToOpen) {
        windowToOpen.isVisible = true;
      }
    };
    provide("openWindow", openWindow);
    provide("isWindowVisible", isWindowVisible);
    provide("toggleWindowVisibility", toggleWindowVisibility);
    provide("bringToFront", bringToFront);
    return {
      windows,
      bringToFront,
      isWindowVisible,
    };
  },
};
</script>
