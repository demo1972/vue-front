<template>
  <div>
    <h1 class="display-4 text-center mt-4">Task List</h1>
    <hr />
    <div class="col-lg-8 offset-lg-2">
      <div class="card mt-4">
        <div class="card-body">
          <div class="input-group">
            <input
              type="text"
              v-model="task"
              class="form-control form-control-lg"
              placeholder="add new task"
            />
            <div class="input-group-append">
              <buton class="btn btn-success btn-lg" v-on:click="addTask">
                Add
              </buton>
            </div>
          </div>
          <br />

          <h5 v-if="tasklist.length == 0">there's no taks to do</h5>

          <ul class="list-group">
            <li
              class="list-group-item d-flex justify-content-between"
              v-for="(task, index) of tasklist"
              :key="index"
            >
              <span class="cursor" v-on:click="editTask(task.id, task)">
                <i
                  v-bind:class="[
                    task.status
                      ? 'fas fa-check-circle text-success'
                      : 'far fa-circle',
                  ]"
                ></i>
              </span>
              {{ task.name }}
              <span class="cursor text-danger" v-on:click="deleteTask(task.id)">
                <i class="fas fa-trash-alt"></i>
              </span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>

  <div class="row justify-content-md-center">
      <button type="button" class="btn btn-primary col-2  " v-on:click="login()" >Login</button>
      <button type="button" class="btn btn-secondary col-2" v-on:click="logout()" >logout</button>


  </div>
</template>

<script>
import axios from "axios";
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Task",
  data() {
    return {
      task: "",
      tasklist: [],
      user: {
        email: "a@a.com",
        password: "admin1",
      },
    };
  },
  methods: {
    addTask() {
      const task = {
        name: this.task,
        status: false,
      };

      axios.post("https://localhost:7001/api/task/", task,this.getHeaderToken()).then((result) => {
        console.log(result.data);
        this.gettasks();
      });
      this.task = "";
    },
    deleteTask(id) {
      axios.delete("https://localhost:7001/api/task/" + id,this.getHeaderToken()).then((result) => {
        console.log(result.data);
        this.gettasks();
      });
    },
    editTask(id, task) {
      axios
        .put("https://localhost:7001/api/task/" + id, task,this.getHeaderToken())
        .then((result) => {
          console.log(result.data);
          this.gettasks();
        });
    },
    gettasks() {
      axios
        .get("https://localhost:7001/api/task", this.getHeaderToken())
        .then((response) => {
          this.tasklist = response.data;
        });
    },
    gettoken(user) {
      axios.post("https://localhost:7001/api/token/", user).then((result) => {
        //console.log(result.data);
        if (result.data.token != "") {
          localStorage.setItem("token", result.data.token);
        }
      });
    },
    getHeaderToken() {
      let token = localStorage.getItem("token");
      return { headers: { Authorization: "Bearer " + token } };
    },
    login(){
        this.gettoken(this.user);
        this.gettasks();
    },
    logout(){
        localStorage.removeItem('token');
        location.reload();
    }
  },
  created: function () {
    
  },
};
</script>

<style  scoped>
.cursor {
  cursor: pointer;
}
</style>