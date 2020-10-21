<template>
  <div id="app">
    <HRTable v-bind:employees="employees" @remove-record="removeRecord" @save-records="saveRecords" />
    <button id="add" @click=addRecord()>Добавить ✚</button>
  </div>
</template>

<script>



import HRTable from "@/components/HRTable"

export default {
  name: 'App',
  data() {
    return {
      employees: JSON.parse(localStorage.getItem('employees')) || [{id: 1, name: '', position: '', salary: '', status: '', recruitmentDate: ''}] // getting all records from localstorage
    }
  },
  components: {
    HRTable
  },
  methods: {
    removeRecord(id) {
      if(confirm('Вы действительно хотите удалить запись?')) {
        this.employees = this.employees.filter(record => record.id != id); // saving to the employees array all records without the deleted one
        this.saveRecords();
      }
    },
    addRecord() {
      let id = Math.max.apply(null, this.employees.map(record=> record.id)) + 1; //max id of records
      this.employees.push({id, name: '', position: '', salary: '', status: '', recruitmentDate: ''}) //adding a new record
      this.saveRecords();
    },
    saveRecords() {
      localStorage.setItem('employees', JSON.stringify(this.employees)) // saving all records to the localstorage
    }
  }
}

</script>

<style lang="less">
  body {
    background: url('./assets/blue-snow.png');
  }
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  button {
    cursor: pointer;
  }

  table {
    margin: 50px auto;
    border-collapse: collapse;
    background-color: white;
    border: 2px solid black;     
    tr {
      &:nth-child(even) {
        background-color: #e8e8e8;
      }
        td,th {
            padding: 5px;
            border-left: 1px dashed #000;      
        }   
        th {
            font-size: 16px;
            font-weight: bold;
            vertical-align: middle;
            background-color: black;
            color: #ffebff;
            text-align: left;
            border-right: 1px dashed #ffebff;
        }
        td {
            input,select {
                border: none;
                width: -webkit-fill-available;
                background-color: transparent;
                font-size: 14px;
            }
            input[type="date"] {
              cursor: pointer;
            }
            select {
                -webkit-appearance: none;
            }
        }
    }
}
</style>



// [
//         {id: 1, name: 'Fedor', position: 'BackEnd dev', salary: '25.000', status: 'Уволен', recruitmentDate: '2020-10-16'},
//         {id: 2, name: 'Rigus', position: 'Web-Designer', salary: '35.000', status: 'Сотрудник', recruitmentDate: '2020-10-16'},
//         {id: 3, name: 'Rigus', position: 'Web-Designer', salary: '35.000', status: 'Сотрудник', recruitmentDate: '2020-10-16'},
//         {id: 4, name: 'Alex', position: 'FrontEnd dev', salary: '25.000', status: 'Соискатель', recruitmentDate: '2020-10-16'}
//       ]