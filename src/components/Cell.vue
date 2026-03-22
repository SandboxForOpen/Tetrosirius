<script setup lang="ts">
import { ref } from "vue";

interface Props {
  isActive: boolean;
  isDeleting?: boolean;
}

interface Emits {
  (e: "toggle"): void;
}

defineProps<Props>();
defineEmits<Emits>();

const isTouching = ref(false);

const handleTouchStart = (): void => {
  isTouching.value = true;
};

const handleTouchEnd = (): void => {
  isTouching.value = false;
};
</script>

<template>
  <div
    class="cell"
    :class="{
      'cell-active': isActive,
      touching: isTouching,
      deleting: isDeleting,
    }"
    @click="$emit('toggle')"
    @touchstart="handleTouchStart"
    @touchend="handleTouchEnd"
  />
</template>

<style scoped>
.cell {
  width: 3.5rem;
  height: 3.5rem;
  border: 1px solid #666666;
  background-color: #f0f0f0;
  cursor: pointer;
  user-select: none;
  margin: -1px;
  transition: opacity 0.2s ease;
}

.cell-active {
  background-color: #333333;
}

/* タッチ中の視覚フィードバック */
.cell.touching {
  opacity: 0.7;
}

/* マウスホバーはデスクトップのみ */
@media (hover: hover) {
  .cell:hover {
    opacity: 0.9;
  }
}

/* 削除アニメーション：白く変化 */
@keyframes deleteCellActive {
  0% {
    background-color: #333333;
  }
  100% {
    background-color: #f0f0f0;
  }
}

@keyframes deleteCellInactive {
  0% {
    background-color: #f0f0f0;
  }
  100% {
    background-color: #f0f0f0;
  }
}

.cell.deleting.cell-active {
  animation: deleteCellActive 2s ease-in-out forwards;
}

.cell.deleting:not(.cell-active) {
  animation: deleteCellInactive 2s ease-in-out forwards;
}
</style>
