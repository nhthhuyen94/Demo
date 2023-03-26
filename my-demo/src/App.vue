<template>
  <v-app>
    <v-main>
      <!-- <HelloWorld/> -->
      <filter-demo />
    </v-main>
  </v-app>
</template>

<script>
import FilterDemo from './components/FilterDemo.vue';
// import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    // HelloWorld,
    FilterDemo,
  },

  data: () => ({
    
  }),
};
</script>
