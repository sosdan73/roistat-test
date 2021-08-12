<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <h1>Vue-test</h1>
    </v-app-bar>
    <v-main>
      <v-container>
        <router-view class="pt-5"/>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',

  data: () => ({
    //
  }),
};
</script>
