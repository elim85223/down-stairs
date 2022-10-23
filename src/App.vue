<script setup>
import DownStairs from "./components/DownStairs.vue";
</script>

<template>
  <DownStairs />
</template>

<style>
.stair {
  width: 100px;
  height: 20px;
  background-color: aqua;
}

.fail {
  width: 200px;
  height: 100px;
  background-color: cornflowerblue;
  position: absolute;
  top: 350px;
  left: 250px;
  display: none;
  flex-direction: column;
  justify-content: space-around;
}
</style>
