<template>
  <v-app>
    <virtual-scroller />
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
import VirtualScroller from './components/VirtualScroller.vue';

export default {
  name: 'App',

  components: { TreeList, TreeListVuetifyCheckbox, VirtualScroller },
};
</script>
, VirtualScroller
