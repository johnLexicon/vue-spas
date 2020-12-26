<template>
  <div id="app">
    <navigation :user="user" @logout="logout" />
    <router-view
      class="container"
      :user="user"
      :meetings="meetings"
      :error="error"
      @add-meeting="addMeeting"
      @delete-meeting="deleteMeeting"
      @check-in="checkIn"
    />
  </div>
</template>

<script>
import Firebase from 'firebase'
import Navigation from './components/Navigation.vue'
import config from './config.js'
import db from './db'
export default {
  name: 'App',
  components: {
    Navigation
  },
  // Usage when the import of db module which inizializes the Firebase App was commented out.
  created() {
    if (Firebase.apps.length === 0) {
      Firebase.initializeApp(config)
    }
  },
  mounted() {
    //const snapshot = await db.collection('users').doc('6RBOM0K3gAMWSECL3J0i').get()
    //this.user = snapshot.data().name

    Firebase.auth().onAuthStateChanged(user => {
      // If user is signed in.
      if (user) {
        this.user = user
        db.collection('users')
          .doc(this.user.uid)
          .collection('meetings')
          //.orderBy('name') // Doesn't work with Uppercase and lower case
          // Checks when there are changes in the meetings collection.
          .onSnapshot(snapshot => {
            const snapshotData = []
            snapshot.forEach(doc =>
              snapshotData.push({
                id: doc.id,
                name: doc.data().name // Retrieves the data from the db
              })
            )
            this.meetings = snapshotData.sort((a, b) => {
              if (a.name.toLowerCase() < b.name.toLowerCase()) {
                return -1
              } else {
                return 1
              }
            })
          })
      }
    })
  },
  data() {
    return {
      user: null,
      meetings: [],
      error: null
    }
  },
  methods: {
    async logout() {
      try {
        await Firebase.auth().signOut()
        this.user = null
        this.$router.push('/login')
      } catch (err) {
        console.error(err.message)
      }
    },
    async addMeeting(meetingName) {
      try {
        db.collection('users')
          .doc(this.user.uid)
          .collection('meetings')
          .add({
            name: meetingName,
            createdAt: Firebase.firestore.FieldValue.serverTimestamp()
          })
      } catch (err) {
        console.error(err.message)
      }
    },
    async deleteMeeting(meetingId) {
      try {
        await db
          .collection('users')
          .doc(this.user.uid)
          .collection('meetings')
          .doc(meetingId)
          .delete()
      } catch (err) {
        console.error(err.message)
      }
    },
    async checkIn(payload) {
      try {
        const doc = await db
          .collection('users')
          .doc(payload.userId)
          .collection('meetings')
          .doc(payload.meetingId)
          .get()
        if (doc.exists) {
          await db
            .collection('users')
            .doc(payload.userId)
            .collection('meetings')
            .doc(payload.meetingId)
            .collection('attendees')
            .add({
              displayName: payload.displayName,
              email: payload.email,
              createdAt: Firebase.firestore.FieldValue.serverTimestamp()
            })
          this.$router.push(`/attendees/${payload.userId}/${payload.meetingId}`)
        } else {
          this.error = 'Sorry, no such meeting'
        }
      } catch (err) {
        console.error(err.message)
      }
    }
  }
}
</script>

<style lang="scss">
$primary: #05b2dd;
@import 'node_modules/bootstrap/scss/bootstrap';
</style>
