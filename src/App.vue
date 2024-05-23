<script setup lang="ts">
import HelloWorld from "./components/HelloWorld.vue";
import VirtualList from "./components/VirtualList.vue";
import list from "./mock";
</script>

<template>
  <div class="container">
    <VirtualList :virtualList="list" :id="'tinyCheckDetailId'">
      <template #list="{ item }">
        <div class="item" :key="item.tinyCheckDetailId">
          {{ item.tinyCheckDetailId + "虚拟列表" }}
        </div>
      </template>
    </VirtualList>
  </div>
</template>

<style scoped>
.container {
  height: 100vh;
  width: 100vw;
}
.item {
  border: 1px solid #ccc;
  background: #f5f5f5;
  box-sizing: border-box;
}
</style>
