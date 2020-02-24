<template>
  <div id="app">
    <TypingPanel msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import TypingPanel from "./components/TypingPanel.vue";

export default {
  name: "App",
  components: {
    TypingPanel
  }
};
</script>