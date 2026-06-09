<script setup lang="ts">
interface Props {
  hasCompleteRows: boolean;
  isClearing: boolean;
}

interface Emits {
  (e: "clear"): void;
}

defineProps<Props>();
defineEmits<Emits>();
</script>

<template>
  <button
    class="clear-button"
    :disabled="!hasCompleteRows || isClearing"
    @click="$emit('clear')"
  >
    ライン削除
  </button>
</template>

<style scoped>
.clear-button {
  width: 100%;
  margin: auto;
  padding: 1.5rem 1rem;
  margin-top: 37vh;
  font-size: 1.5rem;
  font-weight: bold;
  background-color: #4a90e2;
  color: #f0f0f0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s;
  box-sizing: border-box;
  height: auto;
}

.clear-button:hover:not(:disabled) {
  background-color: #357abd;
}

.clear-button:disabled {
  background-color: #ccc;
  color: #999;
  cursor: not-allowed;
  opacity: 0.6;
}
</style>
