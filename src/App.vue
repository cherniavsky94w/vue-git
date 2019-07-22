<template>
  <v-app>
    <v-toolbar app dense>
      <v-toolbar-title>
        <span>Vue-git</span>
        <!-- <span class="font-weight-light">MATERIAL DESIGN</span> -->
      </v-toolbar-title>
      <v-spacer></v-spacer>
    </v-toolbar>

    <v-content>
      <v-container fluid fill-height>
        <v-layout justify-center>
          <repos-list />
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: "App",
  components: {
    ReposList: () => import("./components/ReposList")
  },
  data() {
    return {
      //
    };
  }
};
</script>

<style>
body {
  min-width: 320px;
}
</style>
