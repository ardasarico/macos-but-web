<template>
  <div>
    <div class="relative flex items-end justify-center mb-1.5">
      <div class="absolute z-[-1] bg-white w-full h-full dock-bg"></div>
      <div class="pb-2 flex items-end px-0.5">
        <button
          ondragstart="return false"
          v-for="(app, index) in apps"
          :key="app.id"
          @mouseover="hoverIcon(index)"
          @mouseleave="leaveIcon(index)"
          :class="[
            'app-icon',
            hoveredIndexes.includes(index) ? 'app-animation' : '',
            app.isOpen && app.type == 'app' ? 'active-app' : '',
            {
              'active-animation':
                app.isOpen && app.type === 'app' && app.id !== 1,
            },
            app.id == '1' ? 'active-app' : '',
          ]"
          @click="openSpecifiedWindow(app.id)"
        >
          <img :src="app.icon" alt="asdasd" />
        </button>
        <div class="h-12 w-0.5 bg-white/10 rounded-lg ml-1"></div>
        <button class="app-icon" ondragstart="return false">
          <img class="h-full w-full" src="/app-icons/trash.png" alt="trash" />
        </button>
      </div>
    </div>
  </div>
</template>
<style lang="scss" scoped>
@keyframes active {
  0% {
    transform: translateY(0px);
  }
  20% {
    transform: translateY(-15%);
  }
  40% {
    transform: translateY(0px);
  }
  60% {
    transform: translateY(-15%);
  }
  80% {
    transform: translateY(0px);
  }
}
.active-animation {
  transition: 0.3s;
  animation: active 3s;
}
.active-app {
  &:after {
    content: "";
    position: absolute;
    width: 4px;
    height: 4px;
    background: #fff;
    border-radius: 100%;
    bottom: -3px;
  }
}
.dock-bg {
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  height: 65px;
  background: rgba(74, 74, 74, 0.39);
  box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(15px);
}
.app-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  user-select: none;
  padding-top: 1px;
  width: 56px;
  height: 56px;
  transition:
    width 0.25s,
    height 0.25s;
  border-radius: 12px;
  &:hover {
    width: 72px;
    height: 72px;
  }
  &:active {
    filter: brightness(50%);
  }
}
.app-animation {
  width: 64px;
  height: 64px;
}
</style>
<script setup lang="ts">
import { ref, inject } from "vue";
import windowsData from "@/assets/apps.json";

const bringToFront = inject("bringToFront");
const openWindow = inject("openWindow");
const hoveredIndexes = ref<number[]>([]);
const apps = ref(windowsData);

const hoverIcon = (index: number) => {
  hoveredIndexes.value = [index - 1, index + 1].filter(
    (i) => i >= 0 && i < apps.value.length,
  );
};

const openSpecifiedWindow = (id: string) => {
  const windowToOpen = apps.value.find((w) => w.id === id);
  if (windowToOpen) {
    if (windowToOpen.type == "link") {
      window.open(windowToOpen.link);
    } else {
      windowToOpen.isOpen = true;
      if (openWindow) {
        openWindow(id);
      }
      if (bringToFront) {
        bringToFront(windowToOpen);
      }
    }
  }
};

const leaveIcon = (index: number) => {
  hoveredIndexes.value = [];
};
</script>
