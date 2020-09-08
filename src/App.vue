<template>
  <v-app class="pa-0 ma-0">
    <!-- <v-main> -->
      <PixelSort />
    <!-- </v-main> -->
  </v-app>
</template>

<script>
import PixelSort from "./components/PixelSort";

export default {
  name: "App",
  components: {
    PixelSort,
  },
  data: () => ({}),
  created() {
    this.$vuetify.theme.dark = true;
  },
};
</script>

<style >
body {
  overflow: hidden;
}

::-webkit-scrollbar {
  display: none;
}

</style>
