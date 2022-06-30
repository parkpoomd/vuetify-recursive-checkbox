<template>
  <v-container>
    <v-row no-gutters>
      <v-col cols="12" sm="4">
        <div>
          <label class="d-inline-flex align-center pl-8 pb-2">
            <input
              type="checkbox"
              :checked="statusSelectedAllOrganization"
              @input="handleSelectedAllOrganization($event)"
            />
            <span class="ml-2">Select All</span>
          </label>
          <ul style="list-style: none">
            <tree-list-item
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
              :value="statusSelectedAllEmployee === 'all'"
              :indeterminate="statusSelectedAllEmployee === 'indeterminate'"
              @change="handleSelectedAllEmployee($event)"
            ></v-checkbox>
          </div>
          <!-- <div class="pl-5 pb-2">
            <v-icon v-if="statusSelectedAllEmployee === 'all'">
              mdi-checkbox-outline
            </v-icon>
            <v-icon v-if="statusSelectedAllEmployee === 'indeterminate'">
              mdi-minus-box-outline
            </v-icon>
            <v-icon v-if="statusSelectedAllEmployee === 'blank'">
              mdi-checkbox-blank-outline
            </v-icon>
            <span class="ml-2">Select All</span>
          </div> -->
          <!-- <label class="d-inline-flex align-center pl-6 pb-2">
            <input
              type="checkbox"
              :checked="statusSelectedAllEmployee"
              @input="handleSelectedAllEmployee($event)"
            />
            <span class="ml-2">Select All</span>
          </label> -->
          <ul class="pl-6" style="list-style: none">
            <li v-for="employee in employees" :key="employee.id">
              <label>
                <input
                  type="checkbox"
                  :checked="employee.checked"
                  @input="handleSelectedEmployee($event, employee)"
                />
                <span class="ml-2">
                  {{ employee.name }}
                </span>
              </label>
            </li>
          </ul>
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
import TreeListItem from './TreeListItem.vue';

export default {
  name: 'TreeList',

  components: {
    TreeListItem,
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
    handleSelectedAllOrganization(event) {
      const { checked } = event.target;
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
    handleSelectedAllEmployee(e) {
      const event = {
        target: {
          checked: e,
        },
      };
      const { checked } = event.target;
      this.employees = this.employees.map((employee) => ({
        ...employee,
        checked,
      }));
      this.handleSelectedAllOrganization(event);
    },
    handleSelectedEmployee(event, employee) {
      const { checked } = event.target;
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
