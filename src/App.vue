<script setup lang="ts">
import HelloWorld from "./components/HelloWorld.vue";
import VirtualList from "./components/VirtualList.vue";
import list from "./mock";
</script>

<template>
  <div class="container">
    <VirtualList :virtualList="list">
      <template #list="{ item, index }">
        <div class="item" :key="item.id">{{ index }}</div>
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
  height: 20px;
}
</style>
