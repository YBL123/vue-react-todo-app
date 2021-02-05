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

    <section>
      <div v-if="taskList.length > 0">
        <ul v-for="task in taskList" :key="task.id">
          <li v-bind:class="{completedTask: task.isComplete}">
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
    };
  },
  methods: {
    addTask() {
      if(!this.edit) {
              const newTask = {
        id: Math.random().toString(),
        text: this.formText,
        isComplete: false,
      };
      this.taskList = [...this.taskList, newTask];
      this.formText = '';
        } else if(this.edit) {
          // reassigning state -> this.taskList = this.Tasklist.map...
          this.taskList = this.taskList.map(task => task.id === this.activeTask.id ? {...task, text: this.formText} : task)
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
    },
    deleteTask(id) {
      this.taskList = this.taskList.filter((task) => task.id !== id);
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
