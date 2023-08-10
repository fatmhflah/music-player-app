<template>
  <div class="app-container">
    <music-list></music-list>
  </div>
</template>

<script setup>
import MusicList from "@/components/MusicList.vue";
</script>

<style>
body {
  padding: 30px;
}
.app-container {
  padding: 20px;
  width: 350px;
  height: 620px;
  margin: 0 auto;
}
</style>
