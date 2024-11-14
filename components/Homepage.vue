
<!-- Html -->
<template>
    <div class="container d-flex justify-content-center align-items-center">
      <div class="card mt-3 col-6">
        <div class="card-body">
          <h1 class="text-center">Task List</h1>
          <div
            class="d-flex flex-column flex-sm-row justify-content-between mt-3 mt-sm-5"
          >
            <div class="col-sm-9">
              <input
                type="text"
                class="form-control"
                placeholder="Tambahkan Tugas Baru..."
                v-model="newTask"
                @keyup.enter="addTask"
                :disabled="tasks.length > 4"
              />
            </div>
            <div class="mt-3 mt-sm-0">
              <button
                type="button"
                class="btn btn-primary"
                @click="addTask"
                v-if="tasks.length <= 4"
              >
                Tambah
              </button>
              <p v-else class="message">List sudah mencapai batas</p>
            </div>
          </div>
          <div class="mt-3 mt-sm-5">
            <div
              class="card item-card mt-2"
              v-for="(task, index) in tasks"
              :key="index"
            >
              <div class="card-body">
                <div class="d-flex justify-content-between">
                  <input
                    class="form-check-input"
                    type="checkbox"
                    v-model="task.isDone"
                  />
                  <div>
                    <p class="fw-semibold">{{ task.description }}</p>
                  </div>
                  <div>
                    <button
                      type="button"
                      class="btn btn-danger"
                      @click="deleteTask(index)"
                    >
                      Hapus Tugas
                    </button>
                  </div>
                </div>
              </div>
            </div>
            <hr />
            <div class="d-flex flex-column flex-sm-row justify-content-between">
              <div>
                <p class="fw-bold" v-show="pendingTasks > 0">
                  Kamu mempunyai  {{ pendingTasks }} tugas tertunda.
                </p>
              </div>
              <div>
                <button
                  type="button"
                  class="btn btn-warning"
                  @click="deleteAllTasks"
                  v-show="tasks.length > 0"
                >
                  Hapus Semua
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
</template>
  <!-- Javascript -->
  <script>
  import { ref, onMounted, computed, watch } from "vue";
  
  export default {
    name: "HomePage",
    setup() {
      const newTask = ref("");
      const tasks = ref([
        { description: "Tinjau aktivitas yang tertunda", isDone: false },
        { description: "Menghadiri rapat harian", isDone: false },
      ]);
      //tambah tugas 
      const addTask = () => {
        if (!newTask.value) return;
        tasks.value.unshift({
          description: newTask.value,
          isDone: false,
        });
        localStorage.setItem("tasks", JSON.stringify(tasks.value));
        newTask.value = "";

        alert('Tugas berhasil ditambahkan!');
      };
      //hapus tugas 1 per 1
      const deleteTask = (index) => {
        tasks.value.splice(index, 1);
        localStorage.setItem("tasks", JSON.stringify(tasks.value));

        alert('Tugas Berhasil Dihapus!');
      };

      //hapus seluruh tugas
      const deleteAllTasks = () => {
        tasks.value = [];
        localStorage.removeItem("tasks");

        alert('Tugas Berhasil Dihapus Semuanya!');
      };
  
      const pendingTasks = computed(() => {
        return tasks.value.filter((x) => x.isDone === false).length;
      });

      //batas max untuk menambahkan tugas
      watch(
        tasks,
        () => {
          if (tasks.value.length > 5) {
            alert('Anda telah mencapai jumlah maksimum tugas yang mungkin (5)');
          }
        },
        { deep: true }
      );
  
      onMounted(() => {
        if (localStorage.tasks) {
          tasks.value = JSON.parse(localStorage.getItem("tasks")) || [];
        }
      });
  
      return {
        newTask,
        tasks,
        addTask,
        deleteTask,
        deleteAllTasks,
        pendingTasks,
      };
    },
  };
  </script>
  <!-- Css -->
  <style scoped>
  .card-body h1 {
    color: #56ff13;
  }
  
  .btn-primary {
    background-color: #0059ff;
    border: transparent;
  }
  
  .btn-danger {
    background-color: #ff0000;
    border: transparent;
  }
  
  .btn-warning {
    background-color: #eeff01;
    color: #fff;
    border: transparent;
  }
  
  .message {
    color: green;
    font-weight: bold;
  }
  
  .item-card {
    height: 25%;
    width: 100%;
    background-color: #1cc5db;
    color: #fff;
  }
  </style>
  