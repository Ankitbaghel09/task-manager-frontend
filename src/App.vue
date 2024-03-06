<template>
  <div id="app" class="container">
    <task-list :tasks="tasks" @edit="editTask" @delete="deleteTask"></task-list>
    <button @click="showForm = !showForm" class="btn btn-success">{{ showForm ? 'Cancel' : 'Add Task' }}</button>
    <task-form v-if="showForm" @submit="saveTask" :button-label="formButtonLabel" :task="selectedTask"></task-form>
  </div>
</template>

<script>
import axios from './axios';
import TaskList from './components/TaskList.vue';
import TaskForm from './components/TaskForm.vue';

export default {
  name: 'App',
  components: {
    TaskList,
    TaskForm
  },
  data() {
    return {
      tasks: [],
      selectedTask: null,
      showForm: false
    };
  },
  created() {
    this.fetchTasks();
  },
  methods: {
    async fetchTasks() {
      await axios.get('/tasks')
        .then(response => {
          this.tasks = response.data.data;
        })
        .catch(error => {
          console.error('Error fetching tasks:', error);
        });
    },
    editTask(task) {
      this.selectedTask = task;
      this.showForm = true;
    },
    async deleteTask(taskId) {
      await axios.delete(`/tasks/${taskId}`)
        .then(() => {
          this.tasks = this.tasks.filter(task => task.id !== taskId);
        })
        .catch(error => {
          console.error('Error deleting task:', error);
        });
    },
    async saveTask(taskData) {
      if (this.selectedTask) {
        // Edit existing task
        await axios.put(`/tasks/${this.selectedTask.id}`, taskData)
          .then(() => {
            const index = this.tasks.findIndex(task => task.id === this.selectedTask.id);
            if (index !== -1) {
              this.tasks.splice(index, 1, { id: this.selectedTask.id, ...taskData });
            }
            this.selectedTask = null;
            this.showForm = false;
          })
          .catch(error => {
            console.error('Error updating task:', error);
          });
      } else {
        // Add new task
        await axios.post('/tasks', taskData)
          .then(response => {
            this.tasks.push(response.data.data);
            this.showForm = false;
          })
          .catch(error => {
            console.error('Error adding task:', error);
          });
      }
    }
  },
  computed: {
    formButtonLabel() {
      return this.selectedTask ? 'Update Task' : 'Add Task';
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
