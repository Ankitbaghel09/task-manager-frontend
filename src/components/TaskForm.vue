<template>
  <div class="container mt-5">
    <div class="row justify-content-center">
      <div class="col-md-6"> <!-- Adjust the column width as needed -->
        <form @submit.prevent="submitForm">
          <div class="mb-3">
            <label for="title" class="form-label">Title</label>
            <input type="text" class="form-control" id="title" v-model="formData.title" required>
          </div>
          <div class="mb-3">
            <label for="status" class="form-label">Status</label>
            <select class="form-select" id="status" v-model="formData.status" required>
              <option value="pending">Pending</option>
              <option value="completed">Completed</option>
            </select>
          </div>
          <div class="mb-3">
            <label for="attachment" class="form-label">Attachment</label>
            <input type="file" class="form-control" id="attachment" @change="handleFileUpload">
          </div>
          <button type="submit" class="btn btn-primary">{{ buttonLabel }}</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
    props: {
        task: Object,
        buttonLabel: String
    },
    data() {
        return {
            formData: {
                title: this.task ? this.task.title : '',
                status: this.task ? this.task.status : 'pending',
                attachment: null
            }
        };
    },
    computed: {
        formTitle() {
        return this.task ? 'Edit Task' : 'Add Task';
        }
    },
    methods: {
        submitForm() {
            if (this.formData.attachment && !(this.formData.attachment instanceof File)) {
            alert('The attachment field must be a file.');
            return;
            }

            if (this.formData.attachment) {
            const reader = new FileReader();
            reader.readAsDataURL(this.formData.attachment);
            reader.onload = () => {
                this.formData.attachment = reader.result;
                this.sendFormData();
            };
            } else {
            this.sendFormData();
            }
        },
        sendFormData() {
            console.log(this.formData);
            this.$emit('submit', this.formData);
        },
        handleFileUpload(event) {
            if (event.target.files.length > 0) {
            const file = event.target.files[0];
            this.formData.attachment = file;
            } else {
            this.formData.attachment = null;
            }
        }
    }
};
</script>
