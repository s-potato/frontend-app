<template>
  <div class="relative flex items-top justify-center min-h-screen bg-gray-100 sm:items-center sm:pt-0">

    <div v-if="isModalVisible">
      <!-- Main modal -->
      <div id="modal" tabindex="-1" aria-hidden="true"
        class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full">
        <div class="mt-3 text-center">
          <!--modal content-->
          <div class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white">
            <!--modal header-->
            <div class="flex justify-between items-start rounded-t">
              <button type="button"
                class="text-gray-400 bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg text-sm p-1.5 ml-auto inline-flex items-center dark:hover:bg-gray-600 dark:hover:text-white"
                @click="onToggle">
                <svg aria-hidden="true" class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"
                  xmlns="http://www.w3.org/2000/svg">
                  <path fill-rule="evenodd"
                    d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                    clip-rule="evenodd"></path>
                </svg>
              </button>
            </div>

            <div class="mt-3 text-center">
              <form class="bg-white rounded px-8 pt-6 pb-8 mb-4" @submit.prevent="">
                <div class="mb-4">
                  <label class="block text-gray-700 text-sm font-bold mb-2" for="name">
                    Name
                  </label>
                  <input
                    class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    id="name" type="text" placeholder="Name" v-model="selectedTask.name">
                </div>
                <div class="mb-6">
                  <label class="block text-gray-700 text-sm font-bold mb-2" for="description">
                    Description
                  </label>
                  <input
                    class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    id="description" type="text" placeholder="Description" v-model="selectedTask.description">
                </div>
                <div class="mb-6" v-if="this.$auth.hasScope('admin')">
                  <label class="block text-gray-700 text-sm font-bold mb-2" for="description">
                    Assign to
                  </label>
                  <select v-model="selectedTask.user_id" class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline">
                    <option v-for="(user, i) in users" :key="user.id" :value="user.id">{{user.name}}</option>
                  </select>
                </div>
              </form>

              <div class="items-center px-4 py-3">
                <button @click="onToggle(); addTask(selectedTask)" v-if="isNewTask"
                  class="px-4 py-2 bg-green-500 text-white text-base font-medium rounded-md w-full shadow-sm hover:bg-green-600 focus:outline-none focus:ring-2 focus:ring-green-300">
                  Save
                </button>
                <button @click="onToggle(); updateTask(selectedTask, selectedId)" v-else
                  class="px-4 py-2 bg-orange-500 text-white text-base font-medium rounded-md w-full shadow-sm hover:bg-orange-600 focus:outline-none focus:ring-2 focus:ring-orange-300">
                  Edit
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="w-full">
      <!-- component -->
      <div class="h-100 w-full flex items-center justify-center bg-teal-lightest font-sans">
        <div class="bg-white rounded shadow p-6 m-4 w-full lg:w-5/6 lg:max-w-[75%]">
          <div class="mb-4">
            <h1 class="font-bold text-3xl text-gray-600 text-center">Todo List</h1>
            <button
              class="p-2 ml-4 mr-2 border-2 rounded-lg border-green text-white bg-green-500 hover:bg-green-700 float-right"
              @click="onToggle(); onAdd();">Add</button>
          </div>
          <table class="w-full table-auto">
            <thead>
              <tr>
                <th class="text-grey-darkest font-semibold text-gray-600">
                  Name</th>
                <th class="text-grey-darkest font-semibold text-gray-600">
                  Description</th>
                <th class="text-grey-darkest font-semibold text-gray-600">
                  Creator</th>
                <th class="text-grey-darkest font-semibold text-gray-600">
                  Assign to</th>
                <th class="text-grey-darkest font-semibold text-gray-600">
                  Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(task, i) in tasks" :key="tasks.id">
                <td
                  :class="[task.is_done ? 'line-through text-green-600' : '', `text-grey-darkest font-semibold text-gray-600`]">
                  {{ task.name }}</td>
                <td
                  :class="[task.is_done ? 'line-through text-green-600' : '', `text-grey-darkest font-semibold text-gray-600`]">
                  {{ task.description }}</td>
                <td
                  :class="[task.is_done ? 'line-through text-green-600' : '', `text-grey-darkest font-semibold text-gray-600`]">
                  {{ task.creator.name }}</td>
                <td
                  :class="[task.is_done ? 'line-through text-green-600' : '', `text-grey-darkest font-semibold text-gray-600`]">
                  {{ task.user.name }}</td>
                <td>
                  <button v-if="task.is_done"
                    class="p-2 ml-4 mr-2 border-2 rounded-lg border-green text-white bg-green-500 hover:bg-green-700"
                    @click="updateTask({ ...task, is_done: false }, i)">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                    </svg>
                  </button>

                  <button v-else class="p-2 ml-4 mr-2 border-2 rounded-lg border-grey"
                    @click="updateTask({ ...task, is_done: true }, i)">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                    </svg>
                  </button>

                  <button v-if="$auth.hasScope('admin') || task.creator_id === $auth.user.id"
                    class="p-2 ml-4 mr-2 border-2 rounded-lg border-orange text-white bg-orange-500 hover:bg-orange-700"
                    @click="onToggle(); onEdit(task, i)">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z">
                      </path>
                    </svg>
                  </button>

                  <button v-if="$auth.hasScope('admin') || task.creator_id === $auth.user.id"
                    class="flex-no-shrink p-2 ml-2 border-2 rounded-lg text-red border-red text-white bg-red-500 hover:bg-red-700"
                    @click="deleteTask(task, i)">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16">
                      </path>
                    </svg>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Tasks',
  data() {
    return {
      isNew: true,
      selectedTask: {},
      isOpen: false,
      tasks: [],
      users: [],
    }
  },
  async fetch() {
    this.tasks = (await this.$axios.get(`${this.$axios.defaults.baseURL}tasks`)).data
    if (this.$auth.hasScope('admin'))
      this.users = (await this.$axios.get(`${this.$axios.defaults.baseURL}user`)).data
  },
  computed: {
    isModalVisible() {
      return this.isOpen;
    },
    isNewTask() {
      return this.isNew
    }
  },
  methods: {
    onToggle: function () {
      this.isOpen = !this.isOpen;
    },
    onAdd: function () {
      this.selectedTask = {};
      this.isNew = true
    },
    onEdit: function (task, i) {
      this.selectedTask = Object.assign({}, task);
      this.selectedId = i
      this.isNew = false
    },

    addTask: function (task) {
      let api = `${this.$axios.defaults.baseURL}tasks/`;
      this.$axios.post(api, { ...task }).then(res => {
        this.tasks = res.data
        console.log(res)
      }).catch(error => {
        console.log(error);
      })
    },

    updateTask: function (task) {
      let api = `${this.$axios.defaults.baseURL}tasks/` + task.id;
      this.$axios.put(api, { ...task }).then(res => {
        this.tasks = res.data
        console.log(this.tasks)
      }).catch(error => {
        console.log(error);
      })
    },

    deleteTask: function (task) {
      let api = `${this.$axios.defaults.baseURL}tasks/` + task.id;
      this.$axios.delete(api).then(res => {
        this.tasks = res.data
      }).catch(error => {
        console.log(error);
      })

    },

  },
}
</script>
