<template>
  <div class="container" id="todo-list">
    <div class="row">
      <div class="col-md-6 mx-auto"> 
        <h1 class="text-center">TODO List</h1>
        <form v-on:submit.prevent="addNewTask()"> 
          <label for="tasknameinput">Task Name</label>
          <input 
            v-model="taskname" 
            type="text" 
            class="form-control" 
            id="tasknameinput"
            placeholder="ADD NEW TASK">
          <button 
            class="btn btn-success btn-block mt-3"
            type="submit"
            v-if="this.isEdit == false"
            >Add Task
          </button>
          <button
            v-else
            @click="updateTask()"
            type="button"
            class="btn btn-primary btn-block mt-3"
          >
          Update Task
          </button>
        </form>

        <table class="table">
          <tr v-for="(todo) in todos" :key="todo.id" :task="todo.task">
            <td class="text-left">{{ todo.task }}</td>
            <td class="text-right">
              <button 
                class="btn btn-info" 
                @click="editTask(todo.task, todo.id)">
                Edit
              </button>
              <button 
                class="btn btn-danger" 
                @click="deleteTask(todo.id)">
                Delete
              </button>
            </td>
          </tr>
        </table>



      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';


export default {
 data(){
  return {
    todos: [],
    id: '',
    taskname: '',
    isEdit: false
  }
 },

  mounted(){
    this.getTasks()
  },

  methods: {
    getTasks() {
      axios({
        method: 'GET',
        url: '/api/tasks'
        }).then(
          result => {
            console.log(result.data)
            this.todos = result.data
          }
        ).catch(err => {
          console.log(err);
        })
    },

    
  addNewTask(){
    axios.post('/api/task', {
      task: this.taskname
    }).then((res) => {
      this.taskname = '';
      this.getTasks();
      console.log(res);
    }).catch(err => {
      console.log(err);
    })
  },

  editTask(task, id){
    this.id = id
    this.taskname = task
    this.isEdit = true
  },

  updateTask(id){
    axios.put(`/api/task/${ this.id }`,
      {task: this.taskname}
      ).then((res) => {
        this.taskname = ''
        this.isEdit =false
        this.getTasks()
        console.log(res);
      }).catch(err => console.log(err))
  },

  deleteTask(id) {
    axios.delete(`/api/task/${ this.id }`)
      .then((res) => {
        this.taskname = ''
        this.getTasks()
        console.log(res);
      }).catch(err => console.log(err))
  }
}

}
</script>
