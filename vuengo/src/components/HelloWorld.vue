<template>
  <div class="hello">
    <!-- page title -->
    <h1 class="title">Vuengo</h1>
    <hr>
    <div class="columns">
      <div class="column is-one-third is-offset-one-third">
        <form action="" v-on:submit.prevent="addTask">
          <h2 class="subtitle">Add Task</h2>

          <!-- descriptions field -->
          <div class="field">
            <label for="" class="label">Description</label>
            <div class="control">
              <input type="text" class="input" v-model="descriptions">
            </div>
          </div>
          <!-- end of descriptions field -->

          <!-- status field -->
          <div class="field">
            <label for="" class="label">Status</label>
            <div class="control">
              <div class="select">
                <select name="" id="" v-model="status">
                  <option value="0">To Do</option>
                  <option value="1">Done</option>
                </select>
              </div>
            </div>
          </div>
          <!-- end of status field -->
          
          <!-- submit button -->
          <div class="field is-grouped">
            <div class="control">
              <button class="button is-link">Submit</button>
            </div>
          </div>
          <!-- end of submit button -->
        </form>
      </div>
    </div>

    <div class="columns">
      <div class="column is-half">
        <h2 class="subtitle">To Do</h2>

        <div class="todo">
          <div class="card" v-for="task in tasks" v-if="task.status == 0">
            <div class="card-content">
              <div class="content">
                {{ task.descriptions }}
              </div>
            </div>

            <footer class="card-footer">
              <a class="card-footer-item" v-on:click="setStatus(task.id)">Done</a>
            </footer>
          </div>
        </div>
      </div>

      <div class="column is-half">
        <h2 class="subtitle">Done</h2>

        <div class="done">
          <div class="card" v-for="task in tasks" v-if="task.status == 1">
            <div class="card-content">
              <div class="content">
                {{ task.descriptions }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'HelloWorld',
  data() {
    return {
      tasks: [],
      descriptions: '',
      status: 0
    }
  },
  mounted() {
    this.getTasks();
  },
  methods: {
    getTasks() {
      axios({
        method: 'get',
        url: 'http://localhost:8000/tasks/',
        auth: {
          username: 'superuser',
          password: 'secret-password'
        }
      }).then(response => this.tasks = response.data);
    },

    addTask() {
      if(this.descriptions) {
        axios({
          method: 'post',
          url: 'http://localhost:8000/tasks/',
          data: {
            descriptions: this.descriptions,
            status: this.status
          },
          auth: {
            username: '',
            password: ''
          }
        }).then((response) => {
          let newTask = {'id': response.data.id, 'descriptions': this.descriptions, 'status': parseInt(this.status)}

          this.tasks.push(newTask)

          this.descriptions = ''
          this.status = 0
        }).catch((error)=> {
          console.log(error)
        });
      }
    },

    setStatus(task_id) {
      let descriptions = '';

      for (let i = 0 ; i < this.tasks.length; i++) {
        if (this.tasks[i].id == task_id) {
          this.tasks[i].status = 1
          descriptions = this.tasks[i].descriptions

          break
        }
      }

      axios({
        method: 'put',
        url: 'http://localhost:8000/tasks/'+task_id+'/',
        headers: {
          'Content-type':'application/json'
        },
        data: {
          descriptions: descriptions,
          status: 1
        },
        auth: {
          username: 'superuser',
          password: 'secret-password'
        }
      })
    }
  }
}
</script>

<style scoped>
.select, select {
  width: 100%;
}

.card {
  margin-bottom: 25px;
}

.done {
  opacity: 0.3;
}
</style>


