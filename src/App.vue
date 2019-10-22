<template>
  <div id="app">
    <ClickBuild v-if="appType=='ClickBuild'" />
  </div>
</template>

<script>
import ClickBuild from "./components/ClickBuild";
export default {
  name: "app",
  components: {
    ClickBuild
  },
  data() {
    return {
      appType,
      myArray: [{ id: 0, name: "1" }, { id: 1, name: "2" }]
    };
  }
};
</script>
