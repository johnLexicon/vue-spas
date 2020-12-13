<template>
  <div id="app">
    <navigation/>
    <router-view class="container" :user="user" />
  </div>
</template>

<script>
import Navigation from './components/Navigation.vue'
import db from './db'
export default {
  components:{
    Navigation
  },
  async mounted(){
    const snapshot = await db.collection('users').doc('6RBOM0K3gAMWSECL3J0i').get()
    this.user = snapshot.data().name
  },
  data(){
    return {
      user: null
    }
  },
}
</script>

<style lang="scss">
$primary: #05b2dd;
@import 'node_modules/bootstrap/scss/bootstrap';
</style>
