<template>
  <div id="app">
    <ClickBuild v-if="appType=='ClickBuild'" />
  </div>
</template>

<script>
import ClickBuild from "./components/ClickBuild.vue";

export default {
  name: "app",
  components: {
    ClickBuild
  },
  data() {
    return {
      appType
    };
  }
};
</script>
