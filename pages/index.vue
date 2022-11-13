<template>
  <div>
    <div v-if="this.$auth.loggedIn">
      Hello {{ this.$auth.user.name }}
      <button @click="logout">Logout</button>
      <Tasks />
    </div>
    <div v-else>
      Hello guest
      <NuxtLink to="/login">
        Login
      </NuxtLink>
      <Tutorial />
    </div>
  </div>
</template>

<script>
export default {
  auth: false,
  name: 'IndexPage',

  methods: {
    logout() {
      console.log('hello')
      this.$auth.logout();
      this.$router.push('/')
    }
  }
}
</script>
