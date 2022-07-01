<template>
  <v-container>
    <v-row no-gutters>
      <v-col cols="12" sm="4">
        <div>
          <v-checkbox
            label="Select All"
            :input-value="statusSelectedAllOrganization"
            @change="handleSelectedAllOrganization($event)"
          ></v-checkbox>
          <ul class="pl-0" style="list-style: none">
            <tree-list-vuetify-checkbox-item
              v-for="organization in organizations"
              :key="organization.id"
              :node="organization"
              :handleSelected="handleSelectedOrganization"
            />
          </ul>
        </div>
      </v-col>

      <v-col cols="12" sm="4">
        <div>
          <div>
            <v-checkbox
              label="Select All"
              :input-value="statusSelectedAllEmployee === 'all'"
              :indeterminate="statusSelectedAllEmployee === 'indeterminate'"
              @change="handleSelectedAllEmployee($event)"
            ></v-checkbox>
          </div>
          <RecycleScroller
            style="height: 380px"
            :items="employees"
            :item-size="32"
            key-field="id"
            v-slot="{ item }"
          >
            <ul class="pl-0" style="list-style: none">
              <v-checkbox
                class="mt-2"
                :label="item.name"
                :input-value="item.checked"
                @change="handleSelectedEmployee($event, item)"
                hide-details
              ></v-checkbox>
            </ul>
          </RecycleScroller>
        </div>
      </v-col>

      <v-col cols="12" sm="4">
        {{ employees.filter((e) => e.checked).length }}
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import _ from 'lodash';

import { RecycleScroller } from 'vue-virtual-scroller';
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css';

import TreeListVuetifyCheckboxItem from './TreeListVuetifyCheckboxItem.vue';

export default {
  name: 'VirtualScroller',

  components: { RecycleScroller, TreeListVuetifyCheckboxItem },

  created() {
    fetch('https://jsonplaceholder.typicode.com/photos')
      .then((response) => response.json())
      .then((json) => {
        this.employees = json
          .map((element) => ({
            id: element.id,
            name: element.title.substring(0, 10),
            checked: false,
          }))
          .slice(0, 50);
      });
  },

  data() {
    return {
      statusSelectedAllOrganization: false,
      selectedOrganizations: [],
      organizations: [
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
    };
  },

  computed: {
    statusSelectedAllEmployee() {
      if (_.every(this.employees, ['checked', true])) {
        return 'all';
      } else if (_.some(this.employees, ['checked', true])) {
        return 'indeterminate';
      } else {
        return 'blank';
      }
    },
  },

  methods: {
    handleSelectedAllOrganization(checked) {
      this.statusSelectedAllOrganization = checked;

      this.organizations.forEach((organization) => {
        const node = this.setSelectedAllOrganizationRecursive(organization);
        this.handleSelectedOrganization(node);
      });
    },
    setSelectedAllOrganizationRecursive(node) {
      node.checked = this.statusSelectedAllOrganization;
      if (node.children && node.children.length) {
        node.children.forEach((children) => {
          this.setSelectedAllOrganizationRecursive(children);
        });
      }
      return node;
    },
    handleSelectedAllEmployee(checked) {
      this.employees = this.employees.map((employee) => ({
        ...employee,
        checked,
      }));
      this.handleSelectedAllOrganization(checked);
    },
    handleSelectedEmployee(checked, employee) {
      const { id } = employee;
      this.employees = this.employees.map((employee) => {
        if (employee.id === id) {
          return { ...employee, checked };
        }
        return employee;
      });
    },
    handleSelectedOrganization(node) {
      this.setSelectedOrganizationsRecursive(node);

      this.employees = this.employees.map((employee) => {
        if (this.selectedOrganizations.includes(employee.nodeId)) {
          return { ...employee, checked: true };
        }
        return { ...employee, checked: false };
      });
    },
    setSelectedOrganizationsRecursive(node) {
      if (node.checked) {
        const index = this.selectedOrganizations.findIndex(
          (id) => id === node.id
        );
        if (index === -1) {
          this.selectedOrganizations = [...this.selectedOrganizations, node.id];
        }
      } else {
        this.selectedOrganizations = this.selectedOrganizations.filter(
          (id) => id !== node.id
        );
      }
      if (node.children && node.children.length) {
        node.children.forEach((children) => {
          this.setSelectedOrganizationsRecursive(children);
        });
      }
    },
  },
};
</script>
