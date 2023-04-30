<template>
  <div id="app">
    <h1 class='title' >Lista de Tarefas</h1>
    <div style="display:flex; justify-content: center; margin-bottom: 40px;">
      <input type="text"  v-model="newList" placeholder="Adicione uma tarefa" style="padding: 10px; border-radius: 5px; border: 1px solid #ccc; font-size: 16px; width: 300px">
      <button class="add" @click="addTask" style="margin-left: 10px; padding: 10px 20px; border-radius: 5px;  font-size: 16px">Adicionar</button>
    </div>
    <ul class='task-list'   v-for="(list, index) in lists" :key="index">
      <li @click="list.showOptions = !list.showOptions" class='task'  style="display:flex; justify-content: space-between; align-items: center; padding: 10px; margin-bottom: 10px; background-color: #f2f2f2; border-radius: 5px">
        <span   style="font-size: 16px; cursor: pointer">{{ list.description }}</span>
        <div>
        <button class="add-task" @click="list.showAdd = !list.showAdd" style="font-size: 13px; padding: 5px 10px;">+</button>
        <button class="delete" @click="deleteTask(index)" style="padding: 5px 10px; border-radius: 5px; background-color: #f44336; color: white; font-size: 16px">Excluir</button>
        </div>
        <form>
          <input type="text"  v-model="list.description" placeholder="Descrição" style="padding: 10px; border-radius: 5px; border: 1px solid #ccc; font-size: 16px; width: 300px">
          <button class="update" @click="updateTask(index)" style="margin-left: 10px; padding: 10px 20px; border-radius: 5px;  font-size: 16px">Atualizar</button>
        </form>
      </li>
      <ul v-if=" (typeof list.tasks.description) == 'string' && list.showOptions == true " >
        <li class="task" style="display:flex; justify-content: space-between; align-items: center; padding: 10px; margin-bottom: 10px; background-color: #f2f2f2; border-radius: 5px">
          <span   style="font-size: 16px; cursor: pointer">{{ list.tasks.description }}</span>
          <button class="delete" @click="deleteTask(index)" style="padding: 5px 10px; border-radius: 5px; background-color: #f44336; color: white; font-size: 16px">Excluir</button>
        </li>
      </ul>
    </ul>
    <button @click="consol()">dasds</button>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',
  data() {
    return {
      newList: '',
      newTask: '',
      lists: [],
      tasks: [],
      showOptions: 1

    }
  },
  methods: {
    getTasks(){
      axios.get('http://127.0.0.1:8000/api/tasks')
      .then(response => {
          response.data.map((list) => { 
          list.tasks =  JSON.parse(list.tasks)
          list.showOptions = false
          list.showAdd = false
          console.log(typeof list.tasks); 
          })
          this.lists = response.data;
          this.tasks = response.data.tasks;
        })
      .catch(error => {
          console.log(error);
        });
    },
    addTask() {
      axios.post('http://127.0.0.1:8000/api/tasks', {})
      .then(response => {
          response.data.map((list) => { 
          list.tasks =  JSON.parse(list.tasks)
          list.showOptions = false
          console.log(typeof list.tasks); 
          })
          this.lists = response.data;
          this.tasks = response.data.tasks;
        })
      .catch(error => {
          console.log(error);
        });
    },
    completeTask(index) {
      this.tasks[index].completed = !this.tasks[index].completed;
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    consol(){
      console.log(this.lists);
    }
    
    
  },
  created(){
    this.getTasks()
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

.task {
  width: 80vw;
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

