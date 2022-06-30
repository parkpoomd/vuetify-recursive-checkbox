<template>
  <v-app>
    <tree-list />
  </v-app>
</template>

<script>
import TreeList from './components/TreeList.vue';

export default {
  name: 'App',

  components: { TreeList },
};
</script>
