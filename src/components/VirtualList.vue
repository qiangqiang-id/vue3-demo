<template>
  <div class="container" ref="containerRef" @scroll="containerScroll">
    <div
      class="content-virtual-scroll"
      :style="{ height: `${virtualHeight}px` }"
    >
      <div
        class="content-item"
        :style="{ ...offsetTop(index) }"
        v-for="(item, index) in realList"
        :key="index"
      >
        <slot name="list" :item="item" :index="index" />
      </div>
      <div
        v-if="realList.length > 0"
        class="tips"
        :style="{ ...offsetTop(realList.length) }"
      >
        没有更多了
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, computed, watch } from "vue";
const props = defineProps({
  itemHeight: {
    // 单个高度
    type: Number,
    default: 100,
  },
  virtualList: {
    type: Array,
    default: () => [],
  },
  id: {
    type: String,
    default: "productNo",
  },
  offsetHeight: {
    type: Number,
    default: 75,
  },
  additionalHeight: {
    type: Number,
    default: 100,
  },
});
// const itemHeight = 100;
const containerRef = ref();
const startIndex = ref(0);
const scroll = ref(0); // 初始化初次滚动高度
const list = ref([]);
const endIndex = computed(() => {
  // 8 为每次渲染的最大数量
  return startIndex.value + 8;
});
const insertList = () => {
  const newList = props.virtualList.slice(startIndex.value, endIndex.value);
  list.value.push(...newList);
  list.value = Object.values(
    list.value.reduce((acc, curr) => {
      acc[curr[props.id]] = curr;
      return acc;
    }, {})
  );
};
watch(
  () => props.virtualList,
  (a, b) => {
    list.value = [];
    insertList();
  },
  {
    immediate: true,
  }
);
onMounted(() => {
  containerRef.value.scrollTop = 0;
});

const virtualHeight = computed(
  () => list.value.length * props.itemHeight + props.additionalHeight
);
const realList = computed(() => {
  return list.value.slice(startIndex.value, endIndex.value);
});

watch(endIndex, (val, preVal) => {
  if (val >= list.value.length) {
    insertList();
  }
});

watch(scroll, (val) => {
  startIndex.value = Math.floor(val / 100);
});

function containerScroll(e) {
  const scrollTop = e?.target?.scrollTop || 0;
  scroll.value = scrollTop;
}
function offsetTop(index) {
  const y = `${
    index * props.offsetHeight + scroll.value - (scroll.value % 100)
  }px`;
  return { transform: `translateY(${y})` };
}
</script>

<style lang="scss" scoped>
.container {
  overflow-y: auto;
}
.content-virtual-scroll {
  position: relative;
  top: 0;
  right: 0;
  width: 100%;
}
.content-item {
  position: absolute;
  top: 0;
  left: 0;
  width: 97%;
  padding-left: 6px;
  padding-top: 10px;
}
.tips {
  padding-top: 10px;
  text-align: center;
  color: #ddd;
  font-size: 14px;
}
</style>
