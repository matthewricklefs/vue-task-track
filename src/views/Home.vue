<template>
  <div>
    <AddTask v-if="showAddTask" @add-task="addTask" />

    <Tasks
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";

export default {
  components: {
    Tasks,
    AddTask,
  },
  props: {
    showAddTask: Boolean,
  },
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async addTask(task) {
      const response = await fetch("api/tasks", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(task),
      });

      const data = await response.json();

      this.tasks = [...this.tasks, data];
    },
    async deleteTask(id) {
      const response = await fetch(`api/tasks/${id}`, {
        method: "DELETE",
      });

      response.status === 200
        ? (this.tasks = this.tasks.filter((task) => task.id !== id))
        : alert("Error deleting task");
    },
    async toggleReminder(id) {
      const taskToToggle = await this.fetchTask(id);
      const updTask = {
        ...taskToToggle,
        reminder: !taskToToggle.reminder,
      };

      const response = await fetch(`api/tasks/${id}`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(updTask),
      });

      const data = await response.json();

      this.tasks = this.tasks.map((task) =>
        task.id === id
          ? {
              ...task,
              reminder: data.reminder,
            }
          : task
      );
    },
    async fetchTasks() {
      const response = await fetch("api/tasks");

      const data = await response.json();

      return data;
    },
    async fetchTask(id) {
      const response = await fetch(`api/tasks/${id}`);

      const data = await response.json();

      return data;
    },
  },
  async created() {
    this.tasks = await this.fetchTasks();
  },
};
</script>

<style scoped></style>
