<template>
  <v-app>

    <v-main>
      <router-view/>
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

<style lang="scss">
  ::-webkit-scrollbar {
    width: 10px;
    border-radius: 5px;
  }
  
  ::-webkit-scrollbar-track {
    background: transparent;
  }

  ::-webkit-scrollbar-thumb {
    background: #c0c0c0;
    border-radius: 5px;
  }

  #app{
    background-color: #f6f6f5;
  }
</style>
