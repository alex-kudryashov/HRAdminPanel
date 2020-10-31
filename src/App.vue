<template>
  <div id="app">
    <HRTable v-bind:employees="employees" @remove-record="removeRecord" @update-record="updateRecord" />
    <preloader v-if="isVisible" />
    <button id="add" @click=addRecord()>Добавить ✚</button>
  </div>
</template>

<script>



import HRTable from "@/components/HRTable"
import preloader from "@/components/preloader"

export default {
  name: 'App',
  data() {
    return {
      // employees: JSON.parse(localStorage.getItem('employees')) || [{id: 1, name: '', position: '', salary: '', status: '', recruitmentDate: ''}], // getting all records from localstorage
      employees: null,
      socket: null,
      isVisible: true
    }
  },
  components: {
    HRTable,
    preloader
  },
  created() {
    this.socket = new WebSocket('wss://hr-server-test.herokuapp.com/');
    // this.socket = new WebSocket(location.origin.replace(/^http/, 'ws'));
    this.socket.onopen = e =>{
      console.log('connected');
    };
    this.socket.onmessage = e=>{
      let data = JSON.parse(e.data);
      switch (data.type) {
          case 'postAll':
              this.employees = data.data
              this.destroyPreloader();
              break
          case 'post':
              this.employees.push(data.data)
              break
          case 'delete':
              this.employees = this.employees.filter(record => record._id != data.data); // saving to the employees array all records without the deleted one              
              break
          case 'put':
              let updatable = this.employees.find(record => record._id == data.data._id); 
              updatable.name = data.data.name;
              updatable.position = data.data.position;
              updatable.salary = data.data.salary;
              updatable.status = data.data.status;
              updatable.recruitmentDate = data.data.recruitmentDate;
              break
          default:
      }
      this.saveRecords();
    };
    this.socket.addEventListener('close', e=>{
        console.log('closed');
    }); 
    this.socket.onclose = function(event) {
        if (event.wasClean) {
            console.log('Соединение закрыто чисто');
        } else {
            console.log('Обрыв соединения');
        }
        console.log('Код: ' + event.code + ' причина: ' + event.reason);
    };
    this.socket.onerror = function(error) {
        console.log("Ошибка " + error.message);
    };
  },
  methods: {
    removeRecord(id) {
      if(confirm('Вы действительно хотите удалить запись?')) {
        this.socket.send(JSON.stringify({type: 'delete', id}));
        this.employees = this.employees.filter(record => record._id != id); // saving to the employees array all records without the deleted one
        this.saveRecords();
      }
    },
    addRecord() {
      let data = {_id: 0, name: '', position: '', salary: '', status: '', recruitmentDate: ''};
      this.socket.send(JSON.stringify({type: 'post', data}));
    },
    updateRecord(id) {
      let updatable = this.employees.filter(record => record._id == id)[0];
      this.socket.send(JSON.stringify({type: 'put', data: updatable}));
      this.saveRecords();
    },
    saveRecords() {
      localStorage.setItem('employees', JSON.stringify(this.employees)) // saving all records to the localstorage
    },
    destroyPreloader() {
      this.isVisible = false
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



// вопросы
// 
// Сохраняю все данные в локал стор после любых изменений, что с ними делать дальше? для чего поддержка кеша?
// 
// Как лучше организовать проверку на одновременное редактирование, отправлять сообщение что в данный
// момент поле редактируется и разрешать редактировать только после нажатия на кнопку сохранения или
// убрать эту кнопку и сохранять после любого изменения поля чтобы не предупреждать об одновременном ред.
// 
// Стоит ли учитывать неактуальность данных если они становятся актуальными у всех после изменения
// у кого то одного при нажатии кнопки сохранения
// 
// 