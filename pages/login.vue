<template>
  <div class="h-screen flex flex-col items-center
              justify-center border  rounded">
    <form class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4"  @submit.prevent="userLogin">
      <div class="block text-gray-700 text-2xl">Login</div>
      <div class="block text-red-700 text-sm" v-if="this.errorMsg">{{this.errorMsg}}</div>
      <div class="mb-4">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="email">
          Email
        </label>
        <input
          class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
          id="username" type="text" placeholder="Username" v-model="login.email">
      </div>
      <div class="mb-6">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="password">
          Password
        </label>
        <input
          class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
          id="password" type="password" placeholder="******************" v-model="login.password" >

      </div>
      <div class="flex items-center justify-between">
        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
          type="submit" >
          Login
        </button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      errorMsg: '',
      login: {
        email: '',
        password: ''
      }
    }
  },
  methods: {
    async userLogin() {
      try {
        let response = await this.$auth.login({ data: this.login })
        console.log(response)
      } catch (err) {
        this.errorMsg = err.message
        console.log(err)
      }
    }
  }
}
</script>
