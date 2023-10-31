<template>
  <div v-if="type === 'app'">
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
          class="window__header flex items-center gap-2"
          @mousedown.stop="startDrag"
          @mouseup.stop="stopDrag"
        >
          <div
            class="absolute left-16 w-full h-full"
            @dblclick="maximizeWindow"
          />
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
        <div class="w-full h-full pt-5">
          <iframe
            class="w-full h-full"
            src="https://www.google.com/webhp?igu=1"
          ></iframe>

          <slot />
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
  </div>
  <div v-else-if="type === 'launchpad'">
    <div v-if="windowData.isVisible">x</div>
  </div>
</template>

<style scoped>
.launchpad-open {
  position: fixed;
  width: 100vw;
  height: 100vh;
  background: black;
  top: 0;
  left: 0;
  z-index: 10;
}
.window {
  @apply bg-red-500 flex flex-col overflow-hidden rounded-lg absolute;
  backdrop-filter: blur(50px);
  box-shadow:
    0px 0px 3px 0px rgba(0, 0, 0, 0.55),
    0px 8px 40px 0px rgba(0, 0, 0, 0.25),
    0px 0px 3px 0px rgba(255, 255, 255, 0.1) inset;
}
.window__content {
  @apply relative w-full h-full;
}
.window__header {
  @apply absolute w-full px-2 py-2 bg-[#1E1E1E];
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
</style>

<script>
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
      type: "",
    };
  },
  props: ["windowData", "type"],

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
      this.windowData.isVisible = false;
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
        this.width = window.innerWidth * (2 / 3);
        this.height = window.innerHeight * (2 / 3);
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
