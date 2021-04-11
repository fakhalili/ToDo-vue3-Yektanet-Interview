<template>
    <div class="task-list">
      <!-- <div> {{filtered }}</div> -->
      <ul v-if="tasks.length > 0">
        <li v-for="(task, index) in tasks" :key="index" :value="task" :style="displayTask(index)? '': 'display: none' ">
          <div style="display: flex; height: 50px">
            <div class="task-name" :style="{ 'background-color': task.color }">
              <input
                type="checkbox"
                name=""
                id=""
                class="myCheckbox"
                v-model="task.done"
                @change="$emit('finishTask', index)"/>

              <span :class="{ done: task.done }"> {{ task.title }}</span>
            </div>
            <div class="task-priority">
              <span>
                {{ task.priority.label }}
              </span>
            </div>
            <button class="brush" @click="showPanal(index)" >
              <img src="../assets/images/gtk_color_picker.png" id="brush" alt="" />
            </button>
            <button :disabled="task.done" class="pin" @click="$emit('pinTask', index)">
              <img v-if="!task.pined" src="../assets/images/pin.png" alt="" />
              <img v-else src="../assets/images/orange-pin.png" alt="" />
            </button>
            <button class="delete" @click="$emit('deleteTask', index)">
              <img src="../assets/images/delete.png" alt="" />
            </button>
          </div>
          <div v-show="selectedIndex === index" class="colors" @focusout = "selectedIndex = -1">
            <span
              v-for="color in colors"
              :key="color"
              :style="{ 'background-color': color }"
              title="assign this color to the task"
              @click="$emit('setColor', index, color); fadePanle();"
            ></span>
          </div>
        </li>
      </ul>
      <span
        class="err-msg"
        v-if="
          tasks.filter((a) => a.color == selectedColor).length == 0 &&
          selectedColor
        "
      >
        no task found by this color
      </span>
    </div>

    <div v-if="tasks.length > 0" class="task-filter">
      <span style="margin-right: 15px">filter by colors: </span>
      <span
        v-for="color in colors"
        :key="color"
        class="panel"
        :style="{ 'background-color': color }"
        @click="filterByColor(color)"
        :class="{ selected: selectedColor === color }"
      ></span>
      <button
        style="margin-left: 10px"
        class="blueBtn"
        @click="filterByColor('')"
      >
        reset
      </button>
    </div>
</template>

<script>
export default {
  name: "tasklist",
  data() {
    return {
      selectedIndex: -1,
      colors: [
        "red",
        "blue",
        "orange",
        "green",
        "yellow",
        "black",
        "white",
        "pink",
        "purple",
        "brown",
      ],
      filtered: false,
      selectedColor: "",
    };
  },
  props: {
    tasks: {
      type: Array,
      required: true,
    },
    addTask: {
      type: Boolean
    }
  },
  emits: ['pinTask', 'deleteTask','finishTask', 'setColor'],
  methods: {
    displayTask(index) {
      if (!this.filtered) return true;
      else if (this.filtered && this.tasks[index].color == this.selectedColor)
        return true;
      else return false;
    },
    showPanal(index) {
      if (this.selectedIndex == index) this.fadePanle();
      else this.selectedIndex = index;
    },
    fadePanle() {
      this.selectedIndex = -1;
    },
    filterByColor(color) {
      if (color === "") {
        this.selectedColor = ""; // double click
        this.filtered = false;
      } else {
        this.selectedColor = color;
        this.filtered = true;
      }
    },

    removePanel() {
      document.addEventListener('click', (event) =>{
        if(event.srcElement.id !== "brush" && this.selectedIndex !== -1) {
          this.selectedIndex = -1;
        }
      })
    }
  },
  created() {
this.removePanel();
  },
  computed: {
     tasklength() {
    return this.tasks.length;
     }
  },
  watch: {
      tasklength: function () {
      this.filterByColor('');
    },
  }
};
</script>

<style scoped>
/* task list style */
.task-list {
  padding: 10px 25px;
  max-height: 500px;
  overflow-y: auto;
  min-height: 100px;
}
.task-list ul {
  list-style: none;
  padding: 0;
}
.task-list ul li 
{
  display: flex;
  flex-direction: column;
  margin-bottom: 15px;
  font-size: 20px;
}
.task-list ul li .colors 
{
  display: flex;
  margin-top: 10px 0;
}
.task-list ul li .colors span {
  width: 40px;
  height: 40px;
  cursor: pointer;
}
.task-list ul li .task-name {
  width: 100%;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  border: 1px solid;
  font-size: 20px;
}
.task-list ul li .task-name .done {
  text-decoration: line-through;
}
.task-list ul li .color {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  margin: 0 10px;
}
.task-list ul li .task-priority {
  border: 1px solid;
  width: 120px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.task-list ul li .brush {
  border: 1px solid;
  width: 80px;
}
.task-list ul li .brush img{
  width: 35px;
}
.task-list ul li .pin {
  border: 1px solid;
  width: 80px;
}
.task-list ul li .pin img{
  width: 35px;
}
.task-list ul li .delete {
  border: 1px solid;
  width: 80px;
}
.task-list ul li .delete img {
  width: 35px;
}
.task-list .myCheckbox {
  height: 30px;
  width: 30px;
}
.task-list .err-msg {
  font-size: 18px;
  color: #d41c1c;
  padding: 10px;
  background: #ffdce2;
  border-radius: 10px;
  display: block;
  margin-top: 25px;
}

/* task filter style */

.task-filter {
  display: flex;
  margin: auto;
  align-items: center;
  font-size: 18px;
  justify-content: center;
  width: 100%;
}
.task-filter .panel {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  margin: 5px 0;
  border-radius: 10px;
}
.task-filter .panel.selected {
  border: 5px solid;
  margin-top: 0;
}

</style>
