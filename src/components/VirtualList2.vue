<template>
  <div
    class="virtual-list-container"
    ref="containerRef"
    @scroll="containerScroll"
  >
    <div
      class="content-virtual-scroll"
      :style="{ height: `${virtualHeight}px` }"
    >
      <div
        class="content-item"
        :style="{ ...offsetTop(index + startIndex) }"
        v-for="(item, index) in realList"
        :key="index"
      >
        <slot
          name="list"
          :item="item"
          :index="index + startIndex"
          :key="item.tinyCheckDetailId"
        />
      </div>
    </div>
  </div>
</template>


<script setup lang='ts'>
import { onMounted, ref, computed, watch } from "vue";

/** 可视区域前后展示的预留个数 */
const RESERVE_ITEM = 4;

const props = defineProps({
  itemHeight: {
    type: Number,
    default: 0,
  },
  spacingHeight: {
    type: Number,
    default: 0,
  },
  list: {
    type: Array,
    default: () => [],
  },
});

const startIndex = ref(0);
const scrollTop = ref(0);
const containerRef = ref();
const visualAreaDisplayNumber = ref(0);

/** 单项的总共高度 ，高度 + 间隔 */
const itemTotalHeight = computed(() => {
  return props.itemHeight + props.spacingHeight;
});

/** 计算可视区能够展示数据 */
const virtualHeight = computed(
  () => props.list.length * itemTotalHeight.value - props.spacingHeight
);

onMounted(() => {
  /** 计算可视区能够展示数据 */
  const visualHeight = containerRef.value.offsetHeight;
  visualAreaDisplayNumber.value = Math.ceil(
    visualHeight / itemTotalHeight.value
  );
});

const endIndex = computed(() => {
  const index = startIndex.value + RESERVE_ITEM + visualAreaDisplayNumber.value;

  return index + RESERVE_ITEM > props.list.length
    ? props.list.length
    : index + RESERVE_ITEM;
});

const realList = computed(() => {
  return props.list.slice(startIndex.value, endIndex.value);
});

watch(scrollTop, (val) => {
  const index = Math.floor(val / itemTotalHeight.value);
  startIndex.value = index - RESERVE_ITEM < 0 ? 0 : index - RESERVE_ITEM;
});

function containerScroll(e: MouseEvent) {
  const { scrollTop: top, scrollHeight } = e?.target;
  scrollTop.value = top;

  /**  滚动到底部 */
  if (top + containerRef.value.offsetHeight === scrollHeight) {
    console.log("滚动到底部啦");
  }
}

function offsetTop(index) {
  const y = `${index * props.itemHeight + props.spacingHeight * index}px`;
  return { transform: `translateY(${y})` };
}
</script>


<style lang="scss" scoped>
.virtual-list-container {
  height: 100%;
  width: 100%;
  position: relative;
  overflow-y: auto;
}

.content-virtual-scroll {
  position: absolute;
  top: 0;
  right: 0;
  height: 100%;
  width: 100%;
}

.content-item {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  text-align: center;
}
</style>