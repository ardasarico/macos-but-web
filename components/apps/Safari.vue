<script setup lang="ts">
import { className } from "postcss-selector-parser";
</script>

<template>
  <div class="w-full h-full overflow-hidden">
    <div
      class="bg-[#393637] h-14 shadow-lg flex justify-center items-center w-full py-3"
    >
      <div
        class="absolute z-[999] h-7 w-full w-3/5 max-w-[600px] flex items-center justify-center text-white/50 bg-[#2C292A] text-sm font-[200] gap-2 rounded"
      >
        <svgo-search class="mt-1 opacity-50" filled />
        <p>search or enter website name</p>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss"></style>
