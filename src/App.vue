<script setup lang="ts">
import HelloWorld from "./components/HelloWorld.vue";
import VirtualList from "./components/VirtualList.vue";
import VirtualList2 from "./components/VirtualList2.vue";
import list from "./mock";
</script>

<template>
  <div class="container">
    <!-- <VirtualList :virtualList="list" :id="'tinyCheckDetailId'">
      <template #list="{ item }">
        <div class="item" :key="item.tinyCheckDetailId">
          {{ item.staffName + "虚拟列表" }}
        </div>
      </template>
    </VirtualList> -->

    <VirtualList2 :list="list" :itemHeight="80" :spacingHeight="30">
      <template #list="{ item, index }">
        <div class="item" :key="item.tinyCheckDetailId">
          {{ index + "虚拟列表" }}
        </div>
      </template>
    </VirtualList2>
  </div>
</template>

<style scoped>
.container {
  width: 100%;
  height: 100%;
  padding: 20px 40px;
}
.item {
  border: 1px solid #ccc;
  background: #f5f5f5;
  box-sizing: border-box;
  height: 80px;
}
</style>
