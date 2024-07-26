<template>
  <div class="container">
    <div v-if="isVisible" class="overlay"></div>
    <Modal class="modal" :isVisible="isVisible" :employees="flattenArray(employees)" @setIsVisible="setIsVisible" @setEmployees="setEmployees" />

    <div class="add-block">
      <button class="add-button" @click="setIsVisible">Добавить</button>
      <button class="delete-all-button" @click="deleteData">Отчистить данные</button>
    </div>

    <table class="employee-list-table">
      <thead>
        <tr>
          <th>Имя</th>
          <th>Телефон</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in flattenArray(employees)" :key="index">
          <td :style="{ 'padding-left': item.prefix.split('.').length * 10 + 'px' }">{{ item.prefix + " " + item.name }}</td>
          <td>{{ item.number }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import Modal from './Modal.vue';

export default {
  name: 'Table',
  components: {
    Modal
  },
  data () {
    return {
      isVisible: false,
      employees: []
    };
  },
  mounted () {
    const storedData = localStorage.getItem('employees');
    if (storedData) {
      this.employees = JSON.parse(storedData);
    }

    window.addEventListener('beforeunload', this.saveDataBeforeUnload);
  },
  methods: {
    setIsVisible () {
      this.isVisible = !this.isVisible;
    },
    flattenArray (array) {
      const result = [];
      const recurse = (items, prefix = '1') => {
        for (const [index, item] of items.entries()) {
          let currentPrefix = prefix.includes('.') ? `${prefix}${index + 1}` : `${index + 1}`;
          result.push({ ...item, prefix: currentPrefix });
          if (item.childrens && item.childrens.length) {
            currentPrefix += '.';
            recurse(item.childrens, currentPrefix);
          }
        }
      };
      recurse(array);
      return result;
    },
    setEmployees (newEmployee) {
      if (newEmployee.leader) {
        const search = (items) => {
          for (const item of items) {
            if (item.name === newEmployee.leader) {
              if (!item.childrens) {
                item.childrens = [];
              }
              item.childrens.push(
                {
                  id: this.employees.length,
                  name: newEmployee.name,
                  number: newEmployee.number
                }
              );
              return true;
            }
            if (item.childrens && item.childrens.length) {
              if (search(item.childrens)) {
                return true;
              }
            }
          }
          return false;
        };
        return search(this.employees);
      } else {
        this.employees.push(
          {
            id: this.employees.length,
            name: newEmployee.name,
            number: newEmployee.number
          }
        );
      }
    },
    saveDataBeforeUnload () {
      localStorage.setItem('employees', JSON.stringify(this.employees));
    },
    deleteData () {
      localStorage.setItem('employees', []);
      this.employees = [];
    }
  }
};
</script>

<style scoped>
.container {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0 300px;

  box-sizing: border-box;
}

.add-block {
  width: 100%;

  display: flex;
  justify-content: flex-end;

  padding: 20px 0;
}

.add-block > .add-button {
  padding: 4px 12px;
  margin-right: 8px;
  border-radius: 12px;
  border: 1px solid gray;
  cursor: pointer;
}

.add-block > .delete-all-button {
  border-radius: 12px;
  border: 1px solid gray;
  cursor: pointer;
}

.employee-list-table {
  width: 100%;
  padding: 0;
  margin: 0;
}

.employee-list-table tr {
  text-align: left;
}

.employee-list-table th, td {
  border: 1px solid black;
  padding: 10px 0 10px 10px;
}

.modal {
  z-index: 10;
}

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 9;
}
</style>
