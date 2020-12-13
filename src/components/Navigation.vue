<template>
  <div>
    <nav class="navbar navbar-expand bg-primary navbar-dark">
      <div class="container-fluid">
        <router-link class="navbar-brand" to="/">Meeting Log</router-link>
        <div class="navbar-nav ml-auto">
          <router-link v-if="user" to="/meetings" class="nav-item nav-link"
            >meetings</router-link
          >
          <router-link v-if="!user" to="/login" class="nav-item nav-link"
            >login</router-link
          >
          <router-link v-if="!user" to="/register" class="nav-item nav-link"
            >register</router-link
          >
          <button
            v-if="user"
            @click="onLogout"
            class="nav-item nav-link btn btn-link"
          >
            log out
          </button>
          <img
            v-if="user && user.photoURL"
            :src="user.photoURL"
            class="rounded-circle z-depth-0"
            alt="avatar image"
            height="35"
          />
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  name: 'Navigation.vue',
  props: ['user'],
  emits: ['logout'],
  methods: {
    onLogout() {
      console.log('Works!')
      this.$emit('logout')
    }
  }
}
</script>

<style></style>
