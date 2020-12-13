<template>
  <div>
    <form class="mt-3" @submit.prevent="register">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-lg-8">
            <div class="card bg-light">
              <div class="card-body">
                <h3 class="font-weight-light mb-3">Register</h3>
                <div class="form-row">
                  <div v-if="error" class="col-12 alert alert-danger px-3">
                    {{ error }}
                  </div>
                  <section class="col-sm-12 form-group">
                    <label class="form-control-label sr-only" for="displayName"
                      >Display Name</label
                    >
                    <input
                      class="form-control"
                      type="text"
                      id="displayName"
                      placeholder="Display Name"
                      name="displayName"
                      required
                      v-model="displayName"
                    />
                  </section>
                </div>
                <section class="form-group">
                  <label class="form-control-label sr-only" for="email"
                    >Email</label
                  >
                  <input
                    class="form-control"
                    type="email"
                    id="email"
                    placeholder="Email Address"
                    required
                    name="email"
                    v-model="email"
                  />
                </section>
                <div class="form-row">
                  <section class="col-sm-12 form-group">
                    <label class="form-control-label sr-only" for="photoURL"
                      >Photo URL</label
                    >
                    <input
                      class="form-control"
                      type="text"
                      id="photoURL"
                      placeholder="Photo URL"
                      name="photoURL"
                      v-model="photoURL"
                    />
                  </section>
                </div>
                <div class="form-row">
                  <section class="col-sm-6 form-group">
                    <input
                      class="form-control"
                      type="password"
                      placeholder="Password"
                      v-model="passOne"
                    />
                  </section>
                  <section class="col-sm-6 form-group">
                    <input
                      class="form-control"
                      type="password"
                      required
                      placeholder="Repeat Password"
                      v-model="passTwo"
                    />
                  </section>
                </div>
                <div class="form-group text-right mb-0">
                  <button class="btn btn-primary" type="submit">
                    Register
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </form>
    <p class="text-center mt-2">
      or
      <router-link to="/login">login</router-link>
    </p>
  </div>
</template>

<script>
import Firebase from 'firebase'
export default {
  name: 'Register',
  data() {
    return {
      displayName: '',
      email: '',
      photoURL: null,
      passOne: '',
      passTwo: '',
      error: ''
    }
  },
  watch: {
    passTwo(value) {
      if (this.passOne !== '' && value !== '' && this.passOne !== value) {
        this.error = 'Passwords do not match!'
      } else {
        this.error = ''
      }
    }
  },
  methods: {
    async register() {
      const info = {
        email: this.email,
        password: this.passOne,
        displayName: this.displayName,
        photoURL: this.photoURL
      }
      if (!this.error) {
        try {
          const userCredentials = await Firebase.auth().createUserWithEmailAndPassword(
            info.email,
            info.password
          )
          console.log(userCredentials)
          await userCredentials.user.updateProfile({
            displayName: info.displayName,
            photoURL: info.photoURL
          })
          this.$router.replace('Meetings')
        } catch (err) {
          this.error = err.message
        }
      }
    }
  }
}
</script>
