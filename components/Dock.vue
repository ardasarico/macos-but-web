<script setup lang="ts"></script>

<template>
  <div class="fixed bottom-0 w-full flex items-center justify-center">
    <div class="p-1 flex items-center gap-1.5 pb-3 mb-2 dock">
      <button class="h-[64px] aspect-square select-none">
        <img
          class="h-full w-full"
          src="@/assets/media/app-icons/finder.png"
          alt="finder"
        />
      </button>
      <button class="h-[64px] aspect-square select-none">
        <img
          class="h-full w-full"
          src="@/assets/media/app-icons/finder.png"
          alt="finder"
        />
      </button>
      <button class="h-[64px] aspect-square select-none">
        <img
          class="h-full w-full"
          src="@/assets/media/app-icons/finder.png"
          alt="finder"
        />
      </button>
    </div>
  </div>
</template>

<style scoped lang="scss">
.dock {
  mix-blend-mode: luminosity;
  backdrop-filter: blur(15px);
  border-radius: 16px;
  background: rgba(74, 74, 74, 0.39);
  border: solid 1px rgba(255, 255, 255, 0.2);
  outline: solid 1px rgba(0, 0, 0, 0.4);
}
button {
}
</style>
