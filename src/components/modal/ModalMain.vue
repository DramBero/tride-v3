<template>
  <div>
    <div class="window" :class="{ window_dialogue: dialogue }">
      <Vue3DraggableResizable
        class="window-frame"
        :prevent-deactivation="true"
        class-name-handle="my-handle-class"
        v-model:w="w"
        v-model:h="h"
        v-model:x="x"
        v-model:y="y"
        :initW
        :initH
        :minHeight="320"
        :minWidth="550"
        :drag-handle="'.drag-handle'"
        :parent="true"
      >
        <div class="window-header drag-handle">
          <div class="window-header__left"></div>
          <div class="window-header__name">{{ header }}</div>
          <div class="window-header__right">
            <div class="window-header__close" @click="closeModal">
              <!-- <icon name="times" scale="1.3"></icon> -->
              <TdesignClose />
            </div>
          </div>
        </div>
        <div class="window__content">
          <slot> </slot>
        </div>
      </Vue3DraggableResizable>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useSelectedSpeaker } from '@/stores/selectedSpeaker';
import { useWindowSize } from '@vueuse/core';
import { computed, onMounted, ref } from 'vue';
import TdesignClose from '~icons/tdesign/close';

const initW = 1000;
const initH = 600;

const w = ref<number>(initW);
const h = ref<number>(initH);
const x = ref<number>(0);
const y = ref<number>(0);

const windowSize = useWindowSize();
const getInitCenter = computed(() => {
  const initX = Math.round(windowSize.width.value / 2) - Math.round(initW / 2);
  const initY = Math.round(windowSize.height.value / 2) - Math.round(initH / 2);
  return { initX, initY }
});

onMounted(() => {
  x.value = getInitCenter.value.initX;
  y.value = getInitCenter.value.initY;
})

const props = defineProps({
  modalHide: String,
  header: String,
  dialogue: Boolean,
});

const selectedSpeakerStore = useSelectedSpeaker();
function closeModal() {
  selectedSpeakerStore.setSelectedSpeaker({})
  // this.$store.commit(this.modalHide);
}
</script>

<style lang="scss">
.my-handle-class {
  position: absolute;
  background-color: pink;
  border: 1px solid black;
  height: 14px;
  width: 14px;
  opacity: 0;
  -webkit-transition: all 300ms linear;
  -ms-transition: all 300ms linear;
  transition: all 300ms linear;
}

.my-handle-class-tl {
  top: -5px;
  left: -5px;
  z-index: 1;
  cursor: nw-resize;
}

.my-handle-class-tm {
  top: -5px;
  left: 50%;
  width: 99%;
  transform: translateX(-50%);
  margin-left: -7px;
  cursor: n-resize;
}

.my-handle-class-tr {
  top: -5px;
  right: -5px;
  z-index: 1;
  cursor: ne-resize;
}

.my-handle-class-ml {
  top: 50%;
  margin-top: -7px;
  height: 99%;
  transform: translateY(-49%);
  left: -5px;
  cursor: w-resize;
}

.my-handle-class-mr {
  top: 50%;
  margin-top: -7px;
  height: 99%;
  transform: translateY(-49%);
  right: -5px;
  cursor: e-resize;
}

.my-handle-class-bl {
  bottom: -5px;
  z-index: 1;
  left: -5px;
  cursor: sw-resize;
}

.my-handle-class-bm {
  bottom: -5px;
  left: 50%;
  width: 99%;
  transform: translateX(-49%);
  margin-left: -7px;
  cursor: s-resize;
}

.my-handle-class-br {
  bottom: -5px;
  right: -5px;
  z-index: 1;
  cursor: se-resize;
}

.window {
  &-frame {
    background-color: rgba(0, 0, 0, 0.95);
    box-shadow: 0px 0px 9px 5px rgba(0, 0, 0, 0.15);
    border: 2px solid rgb(202, 165, 96);
    border-radius: 4px;
    overflow: hidden;
    pointer-events: all;
  }
  position: fixed;
  pointer-events: none;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 89;
  font-family: 'Pelagiad';
  font-size: 20px;
  color: rgb(202, 165, 96);
  &_dialogue {
    width: 70%;
    height: 70%;
  }
  &-header {
    background-color: rgba(0, 0, 0, 0);
    display: flex;
    justify-content: center;
    //border: 2px solid rgb(182, 145, 76);
    &__left {
      background-color: rgb(182, 145, 76);
      flex-grow: 1;
    }
    &__right {
      background-color: rgb(182, 145, 76);
      flex-grow: 1;
      position: relative;
    }
    &__name {
      background-color: rgba(0, 0, 0, 0);
      border-top: 2px solid rgb(182, 145, 76);
      border-radius: 2px;
      user-select: none;
      padding: 8px 20px;
      height: 35px;
      transition: all 0.2s ease-in;
    }
    &__close {
      color: black;
      display: flex;
      align-items: center;
      top: 1px;
      height: 99%;
      cursor: pointer;
      position: absolute;
      border: 2px solid rgb(202, 165, 96);
      user-select: none;
      background-color: rgb(202, 165, 96);
      border-radius: 4px;
      padding: 0 10px;
      right: 0px;
      &:hover {
        background-color: rgba(0, 0, 0, 0);
      }
    }
  }
  &__content {
    border: 2px solid rgb(202, 165, 96);
    // padding: 5px;
    height: 100%;
    max-height: calc(100% - 35px);
  }
}

.fadeHeight-enter-active,
.fadeHeight-leave-active {
  transition: all 0.15s cubic-bezier(1, 1, 1, 1);
  opacity: 100;
}

.fadeHeight-enter,
.fadeHeight-leave-to {
  opacity: 0%;
}
</style>
