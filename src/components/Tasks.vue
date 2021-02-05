<template>
  <div>
    <div class="hello">
      {{ msg }}
    </div>
    <form @submit.prevent="addTask">
      <input
        type="text"
        value="formText"
        v-model="formText"
        placeholder="Add a task"
      />
      <button type="submit">Add Task</button>
    </form>
    <small v-if="success">{{ success }}</small>

    <section>
      <div v-if="taskList.length > 0">
        <ul v-for="task in taskList" :key="task.id">
          <li v-bind:class="{ completedTask: task.isComplete }">
            {{ task.text }}
            <button v-on:click="completeTask(task.id)">Complete</button>
            <button v-on:click="deleteTask(task.id)">Remove</button>
            <button v-on:click="editTask(task)">Edit</button>
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'Tasks',
  props: {
    msg: String,
  },
  data() {
    return {
      formText: '',
      taskList: [],
      edit: false,
      activeTask: {},
      success: '',
    };
  },
  // similar to fetch
  created() {
    const data = localStorage.getItem('VUE-TASKS');
    this.taskList = [...JSON.parse(data)];
  },
  methods: {
    addTask() {
      if (!this.edit) {
        const newTask = {
          id: Math.random().toString(),
          text: this.formText,
          isComplete: false,
        };
        this.taskList = [...this.taskList, newTask];
        this.formText = '';
        this.success ='Task has been added'
        const timedSuccessMsg = () =>
          setInterval(() => {
            this.success = '';
          }, 2000);
        timedSuccessMsg();
        clearInterval(timedSuccessMsg);

        localStorage.setItem('VUE-TASKS', JSON.stringify(this.taskList));
      } else if (this.edit) {
        // reassigning state -> this.taskList = this.Tasklist.map...
        this.taskList = this.taskList.map((task) =>
          task.id === this.activeTask.id
            ? { ...task, text: this.formText }
            : task
        );
        localStorage.setItem(
          'VUE-TASKS',
          JSON.stringify(
            this.taskList.map((task) =>
              task.id === this.activeTask.id
                ? { ...task, text: this.formText }
                : task
            )
          )
        );
        this.editing = !this.editing;
        this.formText = '';
      }
    },
    completeTask(id) {
      const taskIndex = this.taskList.findIndex((task) => task.id === id);
      const newList = [...this.taskList];
      newList[taskIndex] = {
        ...newList[taskIndex],
        isComplete: !newList[taskIndex].isComplete,
      };
      this.taskList = newList;
      localStorage.setItem('VUE-TASKS', JSON.stringify(newList));
    },
    deleteTask(id) {
      this.taskList = this.taskList.filter((task) => task.id !== id);
      localStorage.setItem(
        'VUE-TASKS',
        JSON.stringify(
          (this.taskList = this.taskList.filter((task) => task.id !== id))
        )
      );
    },
    editTask(task) {
      this.edit = !this.edit;
      this.activeTask = { ...task };
      this.formText = task.text;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.completedTask {
  color: green;
}
</style>
