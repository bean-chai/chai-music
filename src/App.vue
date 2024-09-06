<!--
 * @Author: chaichai 2787922490@qq.com
 * @Date: 2024-09-06 10:36:06
 * @LastEditors: chaichai 2787922490@qq.com
 * @LastEditTime: 2024-09-06 14:49:47
 * @FilePath: \chai-music\src\App.vue
 * @Description: 
 * 
 * Copyright (c) 2024 by ${git_name_email}, All Rights Reserved. 
-->
<script setup lang="ts">
import HelloWorld from "./components/HelloWorld.vue";
</script>

<template>
  <div>
    <div class="first-music tips-box">已经是第一首啦~</div>
    <HelloWorld />
    <div class="last-music tips-box">已经是最后一首啦~</div>
  </div>
</template>

<style scoped lang="scss">
.tips-box {
  font-size: 18px;
  color: #c3c3c3;
  letter-spacing: 1px;
}
.first-music {
  position: fixed;
  left: 40px;
  top: 50%;
}
.last-music {
  position: fixed;
  right: 40px;
  top: 50%;
}
/* .logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
} */
</style>
