<template>
  <div
    v-if="windowData.isVisible"
    class="window"
    :style="{
      top: y + 'px',
      left: x + 'px',
      width: width + 'px',
      height: height + 'px',
    }"
    :class="{ 'window--fullscreen': isFullscreen }"
  >
    <div class="window__content">
      <div
        @dblclick="maximizeWindow"
        class="window__header flex items-center gap-2 px-2 py-2"
        :class="
          (windowData.headerSolid ? 'bg-[#1E1E1E]' : '',
          windowData.content === 'safari' ? ' h-14 px-5 ' : '')
        "
        @mousedown.stop="startDrag"
        @mouseup.stop="stopDrag"
      >
        <button
          class="window__navigation-button window__navigation-button--red"
          @click="closeWindow"
        ></button>
        <button
          class="window__navigation-button window__navigation-button--yellow"
          @click="minimizeWindow"
        ></button>
        <button
          class="window__navigation-button window__navigation-button--green"
          @click="goFullscreen"
        ></button>
      </div>
      <div
        class="w-full h-full"
        :class="windowData.headerSolid ? 'pt-7' : 'pt-0'"
      >
        <apps-finder v-if="windowData.content === 'finder'" />
        <apps-safari v-if="windowData.content === 'safari'" />
        <apps-settings v-if="windowData.content === 'settings'" />
      </div>
    </div>
    <div
      v-for="dir in [
        'top',
        'bottom',
        'left',
        'right',
        'top-left',
        'top-right',
        'bottom-left',
        'bottom-right',
      ]"
      :class="`window__resizer window__resizer--${dir}`"
      @mousedown.prevent="startResize(dir, $event)"
    ></div>
  </div>
</template>

<script>
import { inject } from "vue";
export default {
  data() {
    return {
      x: 50,
      y: 50,
      width: 800,
      height: 500,
      isDragging: false,
      isResizing: false,
      lastX: 0,
      lastY: 0,
      resizeDirection: "",
      minWidth: 600,
      minHeight: 400,
      isMaximized: false,
      isFullscreen: false,
      previousDimensions: null,
    };
  },
  props: ["windowData"],
  methods: {
    goFullscreen() {
      if (!this.isFullscreen) {
        this.previousDimensions = {
          x: this.x,
          y: this.y,
          width: this.width,
          height: this.height,
        };
        this.$el.classList.add("window-fullscreening");
        this.x = 0;
        this.y = 27;
        this.width = window.innerWidth;
        this.height = window.innerHeight - 27;
        this.isFullscreen = true;
        setTimeout(() => {
          this.$el.classList.remove("window-fullscreening");
        }, 300);
      } else {
        this.$el.classList.add("window-exiting-fullscreen");
        const previous = this.previousDimensions;
        this.x = previous.x;
        this.y = previous.y;
        this.width = previous.width;
        this.height = previous.height;
        this.isFullscreen = false;
        setTimeout(() => {
          this.$el.classList.remove("window-exiting-fullscreen");
        }, 300);
      }
    },
    closeWindow() {
      this.windowData.isVisible = false;
      this.windowData.isOpen = false;
    },
    minimizeWindow() {
      this.$el.classList.add("window-scale");

      setTimeout(() => {
        this.$el.classList.remove("window-scale");
        this.windowData.isVisible = false;
      }, 900);
    },
    startDrag(event) {
      if (this.isFullscreen) {
        return;
      }
      this.isDragging = true;
      this.lastX = event.clientX;
      this.lastY = event.clientY;
      document.addEventListener("mousemove", this.drag);
      document.addEventListener("mouseup", this.stopDrag);
    },
    drag(event) {
      if (!this.isDragging) return;
      if (this.windowData.isMaximized) {
        this.width = window.innerWidth * (2 / 5);
        this.height = window.innerHeight * (3 / 5);
        this.windowData.isMaximized = false;
      }
      const deltaX = event.clientX - this.lastX;
      const deltaY = event.clientY - this.lastY;
      const newX = this.x + deltaX;
      const newY = this.y + deltaY;
      if (
        newX + this.width * 0.5 >= 0 &&
        newX + this.width * 0.5 <= window.innerWidth
      ) {
        this.x = newX;
      }
      if (newY >= 27 && newY + this.height * 0.5 <= window.innerHeight) {
        this.y = newY;
      }
      this.lastX = event.clientX;
      this.lastY = event.clientY;
    },
    stopDrag() {
      this.isDragging = false;
      document.removeEventListener("mousemove", this.drag);
    },
    startResize(direction, event) {
      this.isResizing = true;
      this.resizeDirection = direction;
      this.lastX = event.clientX;
      this.lastY = event.clientY;
      document.addEventListener("mousemove", this.resize);
      document.addEventListener("mouseup", this.stopResize);
    },
    maximizeWindow() {
      if (this.isFullscreen) return;
      if (!this.windowData.isMaximized) {
        this.windowData.previousDimensions = {
          x: this.x,
          y: this.y,
          width: this.width,
          height: this.height,
        };
        // Tam ekran yapma animasyonu sınıfını ekliyoruz
        this.$el.classList.add("window-maximizing");
        this.x = 0;
        this.y = 27;
        this.width = window.innerWidth;
        this.height = window.innerHeight - 27 - 76;
        this.windowData.isMaximized = true;
        // Animasyonun bitiminde sınıfı çıkarıyoruz
        setTimeout(() => {
          this.$el.classList.remove("window-maximizing");
        }, 300);
      } else {
        // Pencere tam ekran ise küçültme animasyonu sınıfını ekliyoruz
        this.$el.classList.add("window-minimizing");
        const previous = this.windowData.previousDimensions;
        this.x = previous.x;
        this.y = previous.y;
        this.width = previous.width;
        this.height = previous.height;
        this.windowData.isMaximized = false;
        // Animasyonun bitiminde sınıfı çıkarıyoruz
        setTimeout(() => {
          this.$el.classList.remove("window-minimizing");
        }, 300);
      }
    },
    resize(event) {
      const deltaX = event.clientX - this.lastX;
      const deltaY = event.clientY - this.lastY;
      let newWidth = this.width;
      let newHeight = this.height;
      if (
        this.resizeDirection.includes("right") &&
        this.x + this.width + deltaX <= window.innerWidth
      ) {
        newWidth = this.width + deltaX;
      }
      if (this.resizeDirection.includes("left")) {
        const potentialWidth = this.width - deltaX;
        if (
          potentialWidth >= this.minWidth &&
          this.x + deltaX >= -this.width * 0.5
        ) {
          this.x += deltaX;
          newWidth = potentialWidth;
        }
      }
      if (
        this.resizeDirection.includes("bottom") &&
        this.y + this.height + deltaY <= window.innerHeight
      ) {
        newHeight = this.height + deltaY;
      }
      if (this.resizeDirection.includes("top")) {
        const potentialHeight = this.height - deltaY;
        if (potentialHeight >= this.minHeight && this.y + deltaY >= 27) {
          this.y += deltaY;
          newHeight = potentialHeight;
        }
      }
      this.width = Math.max(newWidth, this.minWidth);
      this.height = Math.max(newHeight, this.minHeight);
      this.lastX = event.clientX;
      this.lastY = event.clientY;
    },
    stopResize() {
      this.isResizing = false;
      document.removeEventListener("mousemove", this.resize);
    },
  },
  mounted() {},
};
</script>

<style scoped>
.window {
  @apply flex flex-col overflow-hidden rounded-[10px] absolute w-full h-full;
  backdrop-filter: blur(10px);
  background: rgba(38, 36, 37, 0.9);
  box-shadow:
    0px 0px 3px 0px rgba(0, 0, 0, 0.55),
    0px 8px 40px 0px rgba(0, 0, 0, 0.25);
  border: 1px rgba(255, 255, 255, 0.25) solid;
  outline: solid 1px rgba(0, 0, 0, 0.3);
}
.window__content {
  @apply relative w-full h-full;
}
.window__header {
  @apply absolute w-full;
}
.window__navigation-button {
  @apply w-3.5 h-3.5 rounded-full flex items-center justify-center;
}
.window__navigation-button--red {
  @apply bg-red-500;
}
.window__navigation-button--yellow {
  @apply bg-yellow-400;
}
.window__navigation-button--green {
  @apply bg-green-500;
}
.window--fullscreen {
  @apply bg-yellow-400 rounded-none;
}
.window__resizer {
  @apply absolute;
}
.window__resizer--top,
.window__resizer--bottom {
  @apply w-full h-0.5;
}
.window__resizer--left,
.window__resizer--right {
  @apply h-full w-0.5;
}
.window__resizer--top-left,
.window__resizer--top-right,
.window__resizer--bottom-left,
.window__resizer--bottom-right {
  @apply w-2 h-2;
}
.window__resizer--top {
  @apply top-0 left-0 cursor-n-resize;
}
.window__resizer--bottom {
  @apply bottom-0 left-0 cursor-s-resize;
}
.window__resizer--left {
  @apply top-0 left-0 cursor-w-resize;
}
.window__resizer--right {
  @apply top-0 right-0 cursor-e-resize;
}
.window__resizer--top-left {
  @apply top-0 left-0 cursor-nw-resize;
}
.window__resizer--top-right {
  @apply top-0 right-0 cursor-ne-resize;
}
.window__resizer--bottom-left {
  @apply bottom-0 left-0 cursor-sw-resize;
}
.window__resizer--bottom-right {
  @apply bottom-0 right-0 cursor-se-resize;
}
.window-maximizing,
.window-minimizing {
  transition: 0.3s;
}
@keyframes minimize {
  0% {
  }
  100% {
    transform-origin: bottom;
    transform: translateY(75%) translateX(25%);
    opacity: 0;
  }
}
.window-scale {
  animation: minimize 1s;
}
</style>
