<template>
  <div>
    <h2>Task List</h2>
    <table class="table" v-if="tasks.length">
      <thead>
        <tr>
          <th>Title</th>
          <th>Status</th>
          <th>Attachment</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
            <td>{{ task.title }}</td>
            <td>{{ task.status }}</td>
            <td>
                <!-- Need to upload attachment file in s3 bucket then show attachment_url. -->
                <a v-if="task.attachment" :href="task.attachment" target="_blank">Download Attachment</a>
                <span v-else>No attachment</span>
            </td>
            <td>
            <button @click="editTask(task)" class="btn btn-warning  me-2"><i class="bi bi-pencil-fill"></i></button>
            <button @click="deleteTask(task.id)" class="btn btn-danger"><i class="bi bi-trash-fill"></i></button>
          </td>
        </tr>
      </tbody>
    </table>
    <div v-else>
      <p>No tasks found.</p>
    </div>
  </div>
</template>

<script>
export default {
  props: ['tasks'],
  methods: {
    editTask(task) {
      this.$emit('edit', task);
    },
    deleteTask(taskId) {
      this.$emit('delete', taskId);
    }
  }
};
</script>
