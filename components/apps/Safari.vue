<script setup lang="ts">
import { className } from "postcss-selector-parser";
</script>

<template>
  <div class="w-full h-full overflow-hidden">
    <div
      class="bg-[#393637] h-14 shadow-lg flex justify-center items-center w-full py-3"
    >
      <div
        class="absolute z-[999] h-7 w-1/2 w-3/5 max-w-[600px] flex items-center justify-center text-white/50 bg-[#2C292A] text-sm font-[200] gap-2 rounded"
      >
        <svgo-search class="mt-1 opacity-50" filled />
        <p>Search or enter website name</p>
      </div>
    </div>
    <div class="w-full h-[calc(100%-56px)] flex items-center justify-center">
      <div class="w-3/5 h-32 flex flex-col gap-2">
        <p class="text-2xl text-white font-semibold">Favorites</p>
        <div class="flex gap-4 mt-2">
          <div class="flex flex-col items-center">
            <img
              src="/safari-icons/yahoo.png"
              alt="logo"
              class="w-[72px] h-[72px] bg-[#6A686A] rounded-lg"
            />
            <p class="mt-1 text-[12px] text-white font-[300]">Yahoo</p>
          </div>
          <div class="flex flex-col items-center">
            <img
              src="/safari-icons/yahoo.png"
              alt="logo"
              class="w-[72px] h-[72px] bg-[#6A686A] rounded-lg"
            />
            <p class="mt-1 text-[12px] text-white font-[300]">Yahoo</p>
          </div>
          <div class="flex flex-col items-center">
            <img
              src="/safari-icons/yahoo.png"
              alt="logo"
              class="w-[72px] h-[72px] bg-[#6A686A] rounded-lg"
            />
            <p class="mt-1 text-[12px] text-white font-[300]">Yahoo</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss"></style>
