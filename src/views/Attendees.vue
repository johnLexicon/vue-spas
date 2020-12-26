<template>
  <div class="container mt-4">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <h1 class="font-weight-light text-center">Attendees</h1>

        <div class="card bg-light mb-4">
          <div class="card-body text-center">
            <div class="input-group input-group-lg">
              <input
                v-model="searchQuery"
                type="text"
                placeholder="Search Attendees"
                class="form-control"
              />
              <div class="input-group-append">
                <button
                  class="btn btn-sm btn-outline-info"
                  title="Pick a random attendee"
                >
                  <font-awesome-icon icon="random"></font-awesome-icon>
                </button>
                <button
                  class="btn btn-sm btn-outline-info"
                  title="Reset Search"
                >
                  <font-awesome-icon icon="undo"></font-awesome-icon>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="row justify-content-center">
      <div
        v-for="attendee in filteredAttendees"
        :key="attendee.id"
        class="col-8 col-sm-6 col-md-4 col-lg-3 mb-2 p-0 px-1"
      >
        <div class="card">
          <div
            class="card-body px-3 py-2 d-flex align-items-center justify-content-center"
          >
            <div v-if="user && user.uid === userID" class="btn-group pr-2">
              <div
                @click="toggleStar(attendee.id)"
                class="btn btn-small"
                :class="[
                  attendee.star ? 'text-danger' : '',
                  'btn-outline-secondary'
                ]"
              >
                <FontAwesomeIcon icon="star" />
              </div>
              <a
                :href="`mailto:${attendee.email}`"
                class="btn btn-small btn-outline-secondary"
              >
                <FontAwesomeIcon icon="envelope" />
              </a>
              <div
                @click="deleteAttendee(attendee.id)"
                class="btn btn-small btn-outline-secondary"
              >
                <FontAwesomeIcon icon="trash" />
              </div>
            </div>
            <div>{{ attendee.displayName }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import db from '../db'
export default {
  name: 'Attendees',
  props: ['userId', 'meetingId', 'user'],
  components: {
    FontAwesomeIcon
  },
  data() {
    return {
      attendees: [],
      userID: this.userId,
      meetingID: this.meetingId,
      searchQuery: ''
    }
  },
  mounted() {
    const query = db
      .collection('users')
      .doc(this.userID)
      .collection('meetings')
      .doc(this.meetingID)
      .collection('attendees')
    query.onSnapshot(snapshot => {
      const data = []
      snapshot.forEach(doc => {
        data.push({
          id: doc.id,
          email: doc.data().email,
          displayName: doc.data().displayName,
          star: doc.data().star
        })
      })
      this.attendees = data
    })
  },
  computed: {
    filteredAttendees() {
      const filterByName = attendee =>
        attendee.displayName.toLowerCase().match(this.searchQuery.toLowerCase())
      return this.attendees.filter(filterByName)
    }
  },
  methods: {
    toggleStar(attendeeId) {
      if (this.user && this.user.uid === this.userID) {
        const ref = db
          .collection('users')
          .doc(this.userID)
          .collection('meetings')
          .doc(this.meetingID)
          .collection('attendees')
          .doc(attendeeId)

        ref.get().then(doc => {
          const star = doc.data().star
          if (star) {
            ref.update({
              star: !star
            })
          } else {
            ref.update({ star: true })
          }
        })
      }
    },
    deleteAttendee(attendeeId) {
      if (this.user && this.user.uid === this.userID) {
        db.collection('users')
          .doc(this.userID)
          .collection('meetings')
          .doc(this.meetingID)
          .collection('attendees')
          .doc(attendeeId)
          .delete()
      }
    }
  }
}
</script>
