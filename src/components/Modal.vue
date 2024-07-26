<template>
  <form class="modal-container" v-if="isVisible" @submit.prevent="saveData">
    <div class="modal-title-block">
      <span>Добавление пользователя</span>
      <span @click="closeModal">Х</span>
    </div>
    <div class="modal-data-block">
      <div>
        <span>Имя</span>
        <input required v-model="formData.name" />
      </div>
      <div>
        <span>Телефон</span>
        <input required v-model="formData.number" />
      </div>
      <div class="employee-leader-block">
        <span>Начальник</span>
        <select v-model="formData.leader">
          <option class="hidden-option" value="">Пусто</option>
          <option v-for="(item, index) in employees" :key="index">{{ item.name }}</option>
        </select>
      </div>
    </div>
    <div class="modal-save-data-block">
      <button type="submit">Сохранить</button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'Modal',
  props: {
    isVisible: {
      type: Boolean
    },
    employees: {
      type: Array
    }
  },
  methods: {
    saveData () {
      this.$emit('setEmployees', this.formData);
      this.closeModal();
    },
    closeModal () {
      this.$emit('setIsVisible');
      this.formData = {
        name: '',
        number: '',
        leader: ''
      };
    }
  },
  data () {
    return {
      formData: {
        name: '',
        number: '',
        leader: ''
      }
    };
  }
};
</script>

<style>
  .modal-container {
    position: absolute;
    left: calc(50% - 250px);
    top: 25%;

    width: 460px;
    height: 280px;
    display: flex;
    flex-direction: column;
    padding: 10px 20px;

    border: 1px solid black;
    border-radius: 8px;
    background-color: white;
  }

  .modal-title-block {
    display: flex;
    justify-content: space-between;
    padding-bottom: 40px;
  }

  .modal-title-block > * {
    font-weight: bold;
  }

  .modal-title-block > :last-child {
    cursor: pointer;
  }

  .modal-data-block {
    display: flex;
    flex-direction: column;

    padding-bottom: 30px;
  }

  .modal-data-block > * {
    height: 30px;
    display: flex;
    justify-content: space-between;

    padding-bottom: 20px;
  }

  .modal-data-block > .employee-leader-block > select {
    width: 176px;
  }

  .modal-data-block > .employee-leader-block > select > .hidden-option {
    color: rgba(210, 215, 211, 0.9)
  }

  .modal-save-data-block {
    display: flex;
  }

  .modal-save-data-block > button {
    width: 100px;
    height: 30px;
    border: 1px solid gray;
    border-radius: 8px;
    cursor: pointer;
  }
</style>
