<template>
  <div class="task-app">
    <div class="container">
      <label for class="show-all">
        <input type="checkbox" v-model="checked" />
        Show All Tasks
      </label>
      <form @submit.prevent="addTask()" class="form">
        <label class="form-group">
          <i class="bx bx-user-circle"></i>
        </label>
        <label for class="form-group">
          <input
            type="text"
            v-model="newTask"
            name="newTask"
            placeholder="#Task"
            class="form-control"
            autocomplete="disabled"
          />
        </label>
        <button class="btn btn-success" @click="addTask()">Add</button>
      </form>
      <table class="table table-bordered">
        <tbody>
          <tr
            v-for="(task, index) in tasks"
            :key="index"
            :class="`${
              task.status == true
              ? checked == true
                ? '' : 'd-none'
              : ''
            }`"
          >
            <td>
              <label>
                <input type="checkbox" :checked="task.status" @change="doneTask(task)" />
                {{ task.status ? 'Done' : '' }}
              </label>
            </td>
            <td>{{ task.content }}</td>
            <td @click="removeTask(task)">
              <i class="bx bx-trash"></i>
            </td>
          </tr>
        </tbody>
      </table>
      <div class="alert alert-danger text-center" v-if="tasks.length === 0">No Task Found!!</div>

      <div v-if="taskDelete !== 0" class="popup shadow">
        <div class="container">
          <form @submit.prevent="deleteTask(taskDelete)" class="form">
            <label for>Are you sure you want to delete this task ?</label>
            <button class="btn btn-danger">
              <i class="bx bx-trash"></i> Delete
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const newTask = ref("");

const checked = ref("false");

const defaultData = [
  {
    status: true,
    content: "Todo List App Done !!",
  },
  {
    status: false,
    content: "Prepared to review ?",
  },
];

const { $cookies } = useNuxtApp();

const tasksData = $cookies.get("tasks") || defaultData;

const tasks = ref(tasksData);

function addTask() {
  let saveTask = 0;
  tasks.value.forEach((task) => {
    if (task.content === newTask.value) {
      saveTask = 1;
    }
  });

  if (saveTask === 0 && newTask.value !== "") {
    tasks.value.push({
      status: false,
      content: newTask.value,
    });
    newTask.value = "";
    saveTask();
  }

  if (saveTask === 1) {
    alert("This task is already present in Tasks");
    saveTask = 0;
    newTask.value = "";
  }
}

function doneTask(task) {
  task.status = !task.status;
}
const taskDelete = ref(0);
function removeTask(index) {
  taskDelete.value = index;
}

function deleteTask(index) {
  tasks.value.splice(index, 1);
  taskDelete.value = 0;
  saveTask();
}

function saveTask() {
  $cookies.set("tasks", tasks.value);
}

useHead({
  link: [
    {
      rel: "stylesheet",
      href: "https://unpkg.com/boxicons@2.1.2/css/boxicons.min.css",
    },
  ],
});
</script>

<style lang="scss">
.task-app {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100%;
  .container {
    background-color: #fff;
    padding: 1.5rem;
    border-radius: 0.3rem;

    .show-all {
      display: flex;
      align-items: center;
      font-size: 1.6rem;
      input {
        margin-right: 1rem;
        transform: scale(1.5);
      }
    }

    .table {
      tr {
        display: grid;
        grid-template-columns: 5rem 1fr 3rem;
        label {
          display: flex;
          align-items: center;
          justify-content: space-around;
          font-size: 0.8rem;
          input {
            margin-right: 0.5rem;
          }
        }
        td {
          display: flex;
          align-items: center;
          justify-content: space-around;
          &:nth-last-child(1) {
            cursor: pointer;
          }
        }
      }
    }

    .form {
      margin: 1rem 0rem;
      display: grid;
      grid-template-columns: 3rem 1fr 5rem;
      border: 1px solid #eaeaea;
      &-group {
        display: flex;
        align-items: center;
        justify-content: center;
        border-right: 1px solid #eaeaea;
        .bx-user-circle {
          font-size: 1.5rem;
        }
      }
      &-control {
        border-color: transparent;
      }
      .btn {
        border-radius: 0rem;
      }
    }
  }

  .popup {
    position: fixed;
    top: 1rem;
    left: 50%;
    transform: translateX(-50%);
    width: 50%;
    .container {
      padding: 1rem;
      .form {
        display: flex;
        flex-direction: column;
        align-items: center;
        grid-gap: 1rem;
        padding: 1rem;
        .btn {
          width: 20rem;
        }
      }
    }
  }
}
</style>