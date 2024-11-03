<template>
  <div class="app">
    <PoeChatWithMarkdown />
  </div>
</template>

<script setup>
import PoeChatWithMarkdown from './components/PoeChatWithMarkdown.vue'
</script>

<style>
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';

html, body {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
}

#app {
  /* height: 100vh; */
  height: 100%;
  width: 100vw;
}

.app {
  height: 100%;
  width: 100%;
}
</style>
