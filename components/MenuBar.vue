<script setup lang="ts"></script>

<template>
  <div
    class="fixed top-0 left-0 w-full flex items-center menubar text-white h-7 px-4 gap-2 select-none"
  >
    <button class="flex items-center justify-center">
      <svgo-apple class="text-2xl" />
    </button>
    <button class="text-sm p-1">App Name</button>
    <button class="text-sm p-1">Item</button>
    <button class="text-sm p-1">Item</button>
    <button class="text-sm p-1">Item</button>
    <div class="ml-auto text-sm flex items-center gap-2 cursor-pointer px-3">
      22 October Sun 20:03
    </div>
  </div>
</template>

<style scoped lang="scss">
.menubar {
  background: rgba(0, 0, 0, 0.18);
  background-blend-mode: darken;
  backdrop-filter: blur(25px);
}
</style>
