<script setup lang="ts">
import { reactive, watch, computed, ref } from "vue";
import { TransitionGroup } from "vue";
import Cell from "./Cell.vue";
import ClearButton from "./ClearButton.vue";

// 10列 × 20行のグリッド初期化
const COLS = 10;
const ROWS = 20;

interface CellData {
  value: boolean;
}

const grid = reactive<CellData[][]>(
  Array.from({ length: ROWS }, (_, rowIndex) => {
    return Array.from({ length: COLS }, () => ({
      value: rowIndex >= 3,
    })) as CellData[];
  }),
);

// 削除対象の行のインデックス
const toDeleteRowIndices = ref<Set<number>>(new Set());

// クリア中かどうか
const isClearing = ref(false);

// セルをクリック時のトグル処理
const toggleCell = (cell: CellData): void => {
  cell.value = !cell.value;
};

// 1行すべてのセルが黒い行があるかどうかを判定
const hasCompleteRows = computed(() => {
  return grid.some((row: CellData[]) =>
    row.every((cell: CellData) => cell.value === true),
  );
});

// クリアボタンの処理
const clearCompleteRows = (): void => {
  // 1行すべてのセルが黒い行を検出
  const rowsToDelete: number[] = [];
  for (let i = 0; i < grid.length; i++) {
    const row = grid[i];
    if (row && row.every((cell: CellData) => cell.value === true)) {
      rowsToDelete.push(i);
      toDeleteRowIndices.value.add(i);
    }
  }

  // クリア中フラグを立てる
  isClearing.value = true;

  // 2秒後に実際の削除処理を実行
  setTimeout(() => {
    // 逆順で削除（インデックスがずれないため）
    for (let i = rowsToDelete.length - 1; i >= 0; i--) {
      const index = rowsToDelete[i];
      if (typeof index === "number") {
        grid.splice(index, 1);
      }
    }

    // クリア完了後、行数が20以下なら、20行になるまで上に白い行を追加
    const rowsToAdd = ROWS - grid.length;
    for (let i = 0; i < rowsToAdd; i++) {
      const newRow: CellData[] = Array.from({ length: COLS }, () => ({
        value: false,
      })) as CellData[];
      grid.unshift(newRow);
    }

    // 削除対象をクリア
    toDeleteRowIndices.value.clear();

    // クリア中フラグを下ろす
    isClearing.value = false;
  }, 2000);
};

// 最初の3行で1つでも黒いセルがある場合、上に白い行を追加
watch(
  () => grid.map((row: CellData[]) => row.map((cell: CellData) => cell.value)),
  () => {
    if (grid.length > 2) {
      const firstThreeRows = grid.slice(0, 3);
      const hasBlack = firstThreeRows.some((row: CellData[]) =>
        row.some((cell: CellData) => cell.value === true),
      );

      if (hasBlack) {
        const newRow: CellData[] = Array.from({ length: COLS }, () => ({
          value: false,
        })) as CellData[];
        grid.unshift(newRow);
      }
    }
  },
  { deep: true },
);
</script>

<template>
  <div class="container">
    <TransitionGroup name="slide" tag="div" class="grid-container">
      <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="grid-row">
        <Cell
          v-for="(cell, colIndex) in row"
          :key="colIndex"
          :is-active="cell.value"
          :is-deleting="toDeleteRowIndices.has(rowIndex)"
          @toggle="toggleCell(cell)"
        />
      </div>
    </TransitionGroup>
    <ClearButton
      :has-complete-rows="hasCompleteRows"
      :is-clearing="isClearing"
      @clear="clearCompleteRows"
    />
  </div>
</template>

<style scoped>
.container {
  display: flex;
  align-items: flex-start;
}

.grid-container {
  display: inline-block;
  margin: 2rem;
}

.grid-row {
  display: flex;
  gap: 0;
}

.slide-move {
  transition: transform 1s ease-in-out;
}
</style>
