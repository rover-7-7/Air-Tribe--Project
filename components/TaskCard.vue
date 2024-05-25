<template>
  <div class="bg-white p-4 mb-2 rounded shadow">
    <div class="flex justify-between">
      <h3>{{ task.title }}</h3>
      <div class="relative" @click="toggleOptions">
        <button class="handle">...</button>
        <div v-if="showOptions" class="absolute right-0 mt-2 bg-white border border-gray-800 p-2 rounded shadow">
          <button @click="editTask">Edit</button>
          <button @click="deleteTask">Delete</button>
        </div>
      </div>
    </div>
    <!-- Add more task details here -->
  </div>
</template>

<script>
export default {
  props: {
    task: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      showOptions: false
    };
  },
  methods: {
    toggleOptions() {
      this.showOptions = !this.showOptions;
    },
    editTask() {
      // Emit an event to notify the parent component about the edit action
      this.$emit('edit-task', this.task);
    },
    deleteTask() {
      // Emit an event to notify the parent component about the delete action
      this.$emit('delete-task', this.task);
    }
  }
};
</script>
