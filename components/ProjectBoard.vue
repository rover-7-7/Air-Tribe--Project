<template>
  <div class="flex justify-center items-center h-screen">
    <div class="flex w-full ml-12 mr-12">
      <div v-for="(column, index) in columns" :key="index" class="flex-1 mr-2">
        <div class="pl-12 rounded">
          <div class="flex justify-between">
            <div class="flex">
              <h2 :class="getColumnTitleClass(column.title)">
                {{ column.title }}
              </h2>
              <span class="text-gray-500 ml-5">{{ column.tasks.length }}</span>
            </div>
            <span class="text-2xl font-bold" @click="addTask(column.title)">+</span>
          </div>

          <!-- Render an empty drop zone -->
          <div
            class="drop-zone"
            @dragover.prevent
            @drop="dropTask(column.title, column.tasks.length)"
          ></div>

          <div class="grid grid-cols-1 gap-4">
            <!-- Render task cards -->
            <div
              v-for="(task, idx) in column.tasks"
              :key="idx"
              :draggable="isClient"
              @dragstart="dragStart(column.title, idx)"
              @dragover.prevent
              @drop="dropTask(column.title, idx)"
            >
              <TaskCard
                :task="task"
                @edit-task="editTask(task)"
                @delete-task="deleteTask(task)"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import TaskCard from "~/components/TaskCard.vue";

export default {
  components: {
    TaskCard,
  },
  data() {
    return {
      columns: [
        { title: "To Do", tasks: [] },
        { title: "In Progress", tasks: [] },
        { title: "Completed", tasks: [] },
      ],
      draggedTask: null,
      draggedColumn: null,
    };
  },
  computed: {
    // Check if the code is running in the client-side
    isClient() {
      return process.client;
    },
  },
  methods: {
    addTask(status) {
      const title = prompt("Enter task title:");
      if (title) {
        this.columns
          .find((column) => column.title === status)
          .tasks.push({ title });
      }
    },
    editTask(task) {
      const newTitle = prompt("Enter new task title:", task.title);
      if (newTitle !== null) {
        task.title = newTitle;
      }
    },
    deleteTask(task) {
      if (confirm("Are you sure you want to delete this task?")) {
        // Loop through all columns to find and delete the task
        for (const column of this.columns) {
          const index = column.tasks.indexOf(task);
          if (index !== -1) {
            column.tasks.splice(index, 1);
            break; // Exit loop once task is deleted
          }
        }
      }
    },
    getColumnTitleClass(title) {
      switch (title) {
        case "To Do":
          return "text-xl font-semibold mb-4 bg-red-200";
        case "In Progress":
          return "text-xl font-semibold mb-4 bg-yellow-200";
        case "Completed":
          return "text-xl font-semibold mb-4 bg-green-200";
        default:
          return "";
      }
    },
    dragStart(columnTitle, taskIndex) {
      this.draggedTask = this.columns.find(
        (column) => column.title === columnTitle
      ).tasks[taskIndex];
      this.draggedColumn = columnTitle;
      console.log("Dragged task:", this.draggedTask);
      console.log("Dragged from column:", this.draggedColumn);
    },
    dropTask(targetColumn, targetIndex) {
      if (this.draggedTask && this.draggedColumn !== targetColumn) {
        const sourceColumn = this.columns.find(
          (column) => column.title === this.draggedColumn
        );
        const taskIndex = sourceColumn.tasks.indexOf(this.draggedTask);
        sourceColumn.tasks.splice(taskIndex, 1);
        this.columns
          .find((column) => column.title === targetColumn)
          .tasks.splice(targetIndex, 0, this.draggedTask);
      }
      console.log("Dropped task:", this.draggedTask);
      console.log("Dropped into column:", targetColumn);
      this.draggedTask = null;
      this.draggedColumn = null;
    },
  },
};
</script>
