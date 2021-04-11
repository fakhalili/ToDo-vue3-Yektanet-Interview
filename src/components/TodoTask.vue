<template>
<div class="container">
  <div class="todolist">
    <h1>ToDo App</h1>
    <h3 class="header3">{{ title }}</h3>
    <div class="new-task">
      <input
        type="text"
        v-model="newTask"
        @keyup.enter="addTask"
        @blur="nameEmpty = false"
        class="task-name"
        :class="{ 'err-input': nameEmpty }"
        placeholder="New Task"
        autofocus
      />
      <span v-if="nameEmpty" class="err-msg">this filed is required</span>

      <select v-model="selectedPriority" class="priority">
        <option v-for="item in priorities" :key="item.priority" :value="item">
          {{ item.label }}
        </option>
      </select>
      <button @click="addTask" class="blueBtn">Add</button>
    </div>
   <tasklist :tasks="tasks" @setColor="onSetColor" @finishTask="onFinishTask" @deleteTask="onDeleteTask" @pinTask="onPinTask"> </tasklist> 
  </div>
</div>

</template>

<script>
import tasklist from './TaskList.vue'

export default {
  name: "todotask",
    components: {
    tasklist
  }, 
  data() {
    return {
      newTask: "",
      nameEmpty: false,
      tasks: [],
      priorities: [
        { priority: 0, label: "low" },
        { priority: 1, label: "medium" },
        { priority: 2, label: "high" },
      ],
      selectedPriority: null,
    };
  },

  methods: {
    addTask() {
      const task = this.newTask.trim();
      if (task) {
        const x = {
          title: task,
          priority: this.selectedPriority,
          done: 0,
          pined: 0,
          color: "white",
        };
        this.tasks.push(x);
        // sort arrays
        this.sortByPriority();
        this.sortByPin();
        this.sortByStatus();
        this.newTask = "";
      } else {
        this.nameEmpty = true;
      }
    },
    onDeleteTask(index) {
      this.tasks.splice(index, 1);
    },
    onPinTask(index) {
      this.tasks[index].pined = !this.tasks[index].pined;
      this.sortByPriority();
      this.sortByPin();
      this.sortByStatus();
    },
    sortByStatus() {
      this.tasks = this.tasks.sort((a, b) => a.done - b.done);
    },
    sortByPin() {
      this.tasks = this.tasks.sort((a, b) => b.pined - a.pined);
    },
    sortByPriority() {
      this.tasks = this.tasks.sort(
        (a, b) => b.priority.priority - a.priority.priority
      );
    },
    onFinishTask(index) {
      this.tasks[index].pined = 0;
      this.sortByPriority();
      this.sortByPin();
      this.sortByStatus();
    },
    onSetColor(index, color) {
      this.tasks[index].color = color;
      this.selectedIndex = -1;
    },
  },
  created() {
    this.selectedPriority = this.priorities[0];
  },
  computed: {
    title() {
      return (
        "Active Tasks: " +
        this.tasks.filter((a) => a.done == 0).length +
        ",  Completed Tasks: " +
        this.tasks.filter((a) => a.done == 1).length
      );
    },
  },
  watch: {
      newTask: function() {
        this.nameEmpty = false;
      }
  }
};
</script>

<style scoped>
.container {
  color: #2c3e50;
  margin-top: 60px;
  text-align: center;
}
.todolist {
  display: flex;
  flex-direction: column;
  padding: 10px;
  width: 80%;
  margin: auto; 
  border: 1px solid;
  border-radius: 15px;
}
.header3{
  color: gray;
  margin-bottom: 2px;
}
/* new task style */
.new-task {
  display: flex;
  padding: 10px 25px;
  position: relative;
}
.new-task .err-msg {
  position: absolute;
  top: 60px;
  color: red;
}
.new-task .task-name {
  width: 70%;
  font-size: 18px;
}
.new-task .task-name.err-input {
  border: 1px solid red;
}
.new-task .priority {
  width: 30%;
  font-size: 18px;
}
.new-task .priority option {
  font-size: 14px;
}

</style>
