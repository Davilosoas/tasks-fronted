<template>
  <div id="app">
    <h1 class='title' >Lista de Tarefas</h1>
    <div style="display:flex; justify-content: center; margin-bottom: 40px;">
      <input type="text"  class="inputAddList" v-model="newList" placeholder="Adicione uma tarefa" style="padding: 10px; border-radius: 5px; border: 1px solid #ccc; font-size: 16px; width: 300px">
      <button class="add" @click="createList" style="margin-left: 10px; padding: 10px 20px; border-radius: 5px;  font-size: 16px">Adicionar</button>
    </div>
    <ul class='task-list'   v-for="(list, index) in lists" :key="lists.description">
      <div v-if="showEditList == list.id" :key="lists.description" class="list">
        <input type="text"  v-model="newListName" placeholder="Escolha o novo nome da lista!" style="padding: 10px; border-radius: 5px; border: 1px solid #ccc; font-size: 16px; width: 300px">
        <div>
          <button class="edit"   @click="renameList(list.id)" style="padding: 5px 10px; border-radius: 5px; margin-right: 7px; " >Editar</button>
          <button class="delete" @click="changeShowEditList(list.id)" style="padding: 5px 10px; border-radius: 5px;  color: white; ">Cancelar</button>

        </div>
       
      </div>
      <li v-if="showEditList !== list.id" @click="selectList(list.id)" class="list"  >
        <span   style="font-size: 16px; cursor: pointer">{{ list.description }}</span>
        <div>
        <button class="add-task" @click="changeShowEditList(list.id)" style="padding: 5px 10px; border-radius: 5px; margin-right: 7px; ">Editar</button>
        <button class="add-task" @click="changeShowAdd(list.id)" style="font-size: 13px; padding: 5px 10px; margin-right: 6px;">+</button>
        <button class="delete" @click="deleteList(list.id)" style="padding: 5px 10px; border-radius: 5px;  color: white;">Excluir</button>
        </div>
        
      </li>
      <form v-if="showAdd == list.id && showEditList == null" class="task">
          <input type="text"  v-model="newTask" placeholder="Descrição" style="padding: 10px; border-radius: 5px; border: 1px solid #ccc; font-size: 16px; width: 300px">
          <button @click="createTask()" class="add"  style="margin-left: 10px; padding: 10px 20px; border-radius: 5px;  font-size: 16px">adicionar</button>
        </form>
      <ul v-if="selectedList == list.id && showEditList == null && showAdd == null" >
        <div v-for="(task) in filteredTasks(list.id)" :key="tasks.completed">
          <div v-if="showEditTask !== task.id" class="task" style="display:flex; justify-content: space-between; align-items: center; padding: 10px; margin-bottom: 10px;  border-radius: 5px">
            <span  @click="completeTask(task.task_list_id, task.id)" :class="{'completed' : task.completed}" style="font-size: 16px; cursor: pointer;"  >{{ task.description }}</span>
            <div>
              <button class="add-task" @click="changeShowEditTask(task.id)" style="padding: 5px 10px; border-radius: 5px; margin-right: 7px; " >Editar</button>
              <button class="delete" @click="deleteTask(task.task_list_id, task.id)" style="padding: 5px 10px; border-radius: 5px;  color: white;">Excluir</button>
            </div>
           

          </div>
          <div v-if="showEditTask == task.id" class="task">
            <input type="text"  v-model="newTaskName" placeholder="Escolha o novo nome da tarefa!" style="padding: 10px; border-radius: 5px; border: 1px solid #ccc; font-size: 16px; width: 300px">
            <div>
              <button class="edit"   @click="renameTask(task.task_list_id, task.id)" style="padding: 5px 10px; border-radius: 5px;  margin-right: 7px;">Editar</button>
              
              <button class="delete" @click="changeShowEditTask(list.id)" style="padding: 5px 10px; border-radius: 5px;  color: white;" >Cancelar</button>

            </div>

          </div>
        </div>
      </ul>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';
import { get } from 'lodash'
export default {
  name: 'App',
  data() {
    return {
      newList: '',
      newTask: '',
      newListName: '',
      newTaskName: '',
      lists: [],
      tasks: [],
      selectedList: null,
      showAdd: null,
      showEditList: null,
      showAddTask: null,
      showEditTask: null,
    }
  },
  computed: {
    filteredTasks() {
      return function(listId) {
        return this.tasks.filter(task => task.task_list_id == listId);
      }
    }
  },

  methods: {
    changeShowEditTask(task_id) {
      this.showEditTask !== task_id? this.showEditTask = task_id : this.showEditTask = null;
    },
    changeShowEditList(list_id) {
      this.showEditList !== list_id? this.showEditList = list_id : this.showEditList = null;
    },
    changeShowAdd(list_id) {
      this.showAdd !== list_id? this.showAdd = list_id : this.showAdd = null
      console.log(this.showAdd)
    },

    selectList(list_id) {
      this.selectedList !== list_id? this.selectedList = list_id : this.selectedList = null
      console.log(this.selectedList)
    },
    getList(){
      axios.get('http://127.0.0.1:8000/api/list')
      .then(response => {

          this.lists = response.data.lists;
          this.tasks = response.data.tasks;
          console.log(response)
          console.log('lists: ' + this.lists)
          console.log('tasks:'+ this.tasks)
        })
      .catch(error => {
          console.log(error);
        });
    },
    createList() {
      axios.post('http://127.0.0.1:8000/api/list/create', {description: this.newList})
      .then(response => {
          
          
          this.lists = response.data.lists;

          this.tasks = response.data.tasks;
          console.log( this.lists, reponse.data.lists)
        })
      .catch(error => {
          console.log(error);
        });
    },
    renameList(list_id) {
      axios.put('http://127.0.0.1:8000/api/list/rename', { description: this.newListName, id: list_id })
      .then(response => {
        this.lists = response.data.lists;
        this.showEditList = null;
       })
      .catch(error => {
        console.log(error);
      });
    },
    deleteList(list_id) {
      axios.delete('http://127.0.0.1:8000/api/list/delete', { data : {id: list_id }})
    .then(response => {
      this.lists = response.data.lists;
      this.tasks = response.data.tasks;
      console.log(list_id)
      })
   .catch(error => {
    console.log(error);
    });
    },

    completeTask(list_id, task_id) {
      axios.put('http://127.0.0.1:8000/api/task/status', {  task_list_id: list_id, id: task_id })
      .then(response => {
        this.tasks = response.data.tasks;
        console.log(this.tasks)
      })
      
    },
    createTask() {
      console.log(this.newTask),
      axios.post('http://127.0.0.1:8000/api/task/create', { description: this.newTask, id: this.showAdd })
      .then(response => {
       
          this.lists = response.data.lists;
          this.tasks = response.data.tasks;
        })
     .catch(error => {
      console.log(error);
      });
    },
    renameTask(list_id, task_id) {
      axios.put('http://127.0.0.1:8000/api/task/rename', { description: this.newTaskName, task_list_id: list_id ,id: task_id })
     .then(response => {
      this.tasks = response.data.tasks;
      this.changeShowEditTask(task_id);
      })
      .catch(error => {
      console.log(error);
      });
    },

    deleteTask(list_id, task_id) {
      axios.delete('http://127.0.0.1:8000/api/task/delete', { data: {task_list_id: list_id, id: task_id }})
      .then(response => {
        this.tasks = response.data.tasks;
       })
      .catch(error => {
        console.log(error);
      });
      },
 
    consol(){
      console.log(this.lists);
    }
    
    
  },
  created(){
    this.getList()
  }
}
</script>

<style>

.title {
  text-align: center; 
  margin-bottom: 30px; 
  margin-top: 40px;
  font-size:  40px;
  color: #39bda7;
}

.inputAddList {
  border-color: #ff0101;
  border-width: 200px;
}
.completed {
  text-decoration: line-through;
  text-decoration-color: #190974;
  color: #383838;
}

#app{
  /* background-color: aquamarine; */
  height: 100vh;
  width: 100vw;
}

.task-list {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  

}

.task, .list {
  width: 80vw;
  display:flex;
  justify-content: space-between;
  align-items: center; 
  padding: 10px; 
  margin-bottom: 10px;
  border-radius: 5px;
}

.task {
  background-color: #d5f3e7;
}
.list {
  background-color: #c7dfe6;
}
.add {
 display: inline-block;
 padding: 12px 24px;
 border: 1px solid #4f4f4f;
 border-radius: 4px;
 transition: all 0.2s ease-in;
 position: relative;
 overflow: hidden;
 font-size: 19px;
 color: black;
 z-index: 1;
}

.add:before {
 content: "";
 position: absolute;
 left: 50%;
 transform: translateX(-50%) scaleY(1) scaleX(1.25);
 top: 100%;
 width: 140%;
 height: 180%;
 background-color: rgba(0, 0, 0, 0.05);
 border-radius: 50%;
 display: block;
 transition: all 0.5s 0.1s cubic-bezier(0.55, 0, 0.1, 1);
 z-index: -1;
}

.add:after {
 content: "";
 position: absolute;
 left: 55%;
 transform: translateX(-50%) scaleY(1) scaleX(1.45);
 top: 180%;
 width: 160%;
 height: 190%;
 background-color: #39bda7;
 border-radius: 50%;
 display: block;
 transition: all 0.5s 0.1s cubic-bezier(0.55, 0, 0.1, 1);
 z-index: -1;
}

.add:hover {
 color: #ffffff;
 border: 1px solid #39bda7;
}

.add:hover:before {
 top: -35%;
 background-color: #39bda7;
 transform: translateX(-50%) scaleY(1.3) scaleX(0.8);
}

.add:hover:after {
 top: -45%;
 background-color: #39bda7;
 transform: translateX(-50%) scaleY(1.3) scaleX(0.8);
}

.delete {
 font-family: 'Ropa Sans', sans-serif;
    /* font-family: 'Valorant', sans-serif; */
 color: white;
 cursor: pointer;
 font-size: 13px;
 font-weight: bold;
 letter-spacing: 0.05rem;
 border: 1px solid #0E1822;
 padding: 0.8rem 2.1rem;
 background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 531.28 200'%3E%3Cdefs%3E%3Cstyle%3E .shape %7B fill: %23FF4655 /* fill: %230E1822; */ %7D %3C/style%3E%3C/defs%3E%3Cg id='Layer_2' data-name='Layer 2'%3E%3Cg id='Layer_1-2' data-name='Layer 1'%3E%3Cpolygon class='shape' points='415.81 200 0 200 115.47 0 531.28 0 415.81 200' /%3E%3C/g%3E%3C/g%3E%3C/svg%3E%0A");
 background-color: #0E1822;
 background-size: 200%;
 background-position: 200%;
 background-repeat: no-repeat;
 transition: 0.3s ease-in-out;
 transition-property: background-position, border, color;
 position: relative;
 z-index: 1;
 text-decoration: none;
}

.delete:hover {
 border: 1px solid #FF4655;
 color: white;
 background-position: 40%;
}

.delete:before {
 content: "";
 position: absolute;
 background-color: #0E1822;
 width: 0.2rem;
 height: 0.2rem;
 top: -1px;
 left: -1px;
 transition: background-color 0.15s ease-in-out;
}

.delete:hover:before {
 background-color: white;
}

.delete:hover:after {
 background-color: white;
}

.delete:after {
 content: "";
 position: absolute;
 background-color: #FF4655;
 width: 0.3rem;
 height: 0.3rem;
 bottom: -1px;
 right: -1px;
 transition: background-color 0.15s ease-in-out;
}





</style>

