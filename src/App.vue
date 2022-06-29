<template>
  <v-app>
    <v-container class="grey lighten-5">
      <v-row no-gutters>
        <v-col cols="12" sm="4">
          <ul class="pr-6" style="list-style: none">
            <node-item
              v-for="node in nodes"
              :key="node.id"
              :node="node"
              :handleSelected="handleSelected"
            ></node-item>
          </ul>
        </v-col>

        <v-col cols="12" sm="4">
          <ul class="pl-6" style="list-style: none">
            <li v-for="employee in filterEmployees" :key="employee.id">
              <label>
                <input type="checkbox" :checked="employee.checked" />
                <span class="ml-2">
                  {{ employee.name }}
                </span>
              </label>
            </li>
          </ul>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import NodeItem from './components/NodeItem.vue';

export default {
  name: 'App',

  components: { NodeItem },

  data() {
    return {
      selected: [],
      employees: [
        { id: '1', name: 'name 1', checked: false, nodeId: '1' },
        { id: '2', name: 'name 1', checked: false, nodeId: '1' },
        { id: '3', name: 'name 1.1', checked: false, nodeId: '1.1' },
        { id: '4', name: 'name 1.1.1', checked: false, nodeId: '1.1.1' },
        { id: '5', name: 'name 2', checked: false, nodeId: '2' },
        { id: '6', name: 'name 2.1', checked: false, nodeId: '2.1' },
        { id: '7', name: 'name 2.1', checked: false, nodeId: '2.1' },
        { id: '8', name: 'name 2.2', checked: false, nodeId: '2.2' },
        { id: '9', name: 'name 2.3', checked: false, nodeId: '2.3' },
        { id: '10', name: 'name 2.3', checked: false, nodeId: '2.3' },
      ],
      nodes: [
        {
          id: '1',
          label: 'Folder 1',
          checked: false,
          children: [
            {
              id: '1.1',
              label: 'Folder 1.1',
              checked: false,
              children: [
                { id: '1.1.1', label: 'Folder 1.1.1', checked: false },
              ],
            },
          ],
        },
        {
          id: '2',
          label: 'Folder 2',
          checked: false,
          children: [
            {
              id: '2.1',
              label: 'Folder 2.1',
              checked: false,
            },
            {
              id: '2.2',
              label: 'Folder 2.2',
              checked: false,
            },
            {
              id: '2.3',
              label: 'Folder 2.3',
              checked: false,
            },
          ],
        },
      ],
    };
  },

  computed: {
    filterEmployees() {
      let items = this.employees;

      if (this.selected.length === 0) {
        items = items.map((item) => ({ ...item, checked: false }));
      } else {
        items = this.employees.map((employee) => {
          if (this.selected.includes(employee.nodeId)) {
            return { ...employee, checked: true };
          }
          return employee;
        });
      }

      return items;
    },
  },

  methods: {
    handleSelected(node) {
      this.callRecursively(node);
    },
    callRecursively(node) {
      if (node.checked) {
        const index = this.selected.findIndex((id) => id === node.id);
        if (index === -1) {
          this.selected = [...this.selected, node.id];
        }
      } else {
        this.selected = this.selected.filter((id) => id !== node.id);
      }
      if (node.children && node.children.length) {
        node.children.forEach((childNode) => {
          this.callRecursively(childNode);
        });
      }
    },
  },
};
</script>
