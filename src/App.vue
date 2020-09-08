<template>
  <v-app class="pa-0 ma-0">
    <PixelSort />
  </v-app>
</template>

<script>
import PixelSort from "./components/PixelSort";

export default {
  name: "App",
  components: {
    PixelSort,
  },
};
</script>

<style >
/* body {
  overflow: hidden;
}

::-webkit-scrollbar {
  display: none;
} */
</style>
