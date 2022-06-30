<template>
  <v-app>
    <div>
      <tree-list />
    </div>
    <div class="mt-8">
      <tree-list-vuetify-checkbox />
    </div>
  </v-app>
</template>

<script>
import TreeList from './components/TreeList.vue';
import TreeListVuetifyCheckbox from './components/TreeListVuetifyCheckbox.vue';

export default {
  name: 'App',

  components: { TreeList, TreeListVuetifyCheckbox },
};
</script>
