<template>
  <div class="container mt-4">
    <div class="row justify-content-center">
      <div class="col-12 col-md-9 col-lg-7">
        <h1 class="font-weight-light text-center">Add a Meeting</h1>

        <div class="card bg-light">
          <div class="card-body text-center">
            <form class="formgroup">
              <div class="input-group input-group-lg">
                <input
                  v-model="meetingName"
                  ref="meetingName"
                  type="text"
                  class="form-control"
                  name="meetingName"
                  placeholder="Meeting name"
                  aria-describedby="buttonAdd"
                />
                <div class="input-group-append">
                  <button
                    @click.prevent="handleAdd"
                    type="submit"
                    class="btn btn-sm btn-info"
                    id="buttonAdd"
                  >
                    +
                  </button>
                </div>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>

    <div class="row justify-content-center">
      <div class="col-11 col-md-8 col-lg-6">
        <div class="card border-top-0 rounded-0">
          <div class="card-body py-2">
            <h4 class="card-title m-0 text-center">Your Meetings</h4>
          </div>
          <div class="list-group list-group-flush">
            <div
              v-for="meeting in meetings"
              :key="meeting.id"
              class="list-group-item d-flex"
            >
              <section
                class="btn-group align-self-center"
                role="group"
                aria-label="Meeting Options"
              >
                <button
                  @click="$emit('delete-meeting', meeting.id)"
                  class="btn btn-sm btn-outline-secondary"
                  title="Delete Meeting"
                >
                  <font-awesome-icon class="text-primary" icon="trash" />
                </button>

                <router-link
                  to="/"
                  class="btn btn-sm btn-outline-secondary"
                  title="Check In"
                >
                  <font-awesome-icon class="text-primary" icon="link" />
                </router-link>

                <router-link
                  to="/"
                  class="btn btn-sm btn-outline-secondary"
                  title="Attendees"
                >
                  <font-awesome-icon class="text-primary" icon="list-ul" />
                </router-link>
              </section>

              <section class="pl-3 text-left align-self-center">
                {{ meeting.name }}
              </section>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
export default {
  name: 'Meetings',
  props: ['user', 'meetings'],
  emits: ['add-meeting', 'delete-meeting'],
  components: {
    FontAwesomeIcon
  },
  data() {
    return {
      meetingName: null
    }
  },
  methods: {
    handleAdd() {
      this.$emit('add-meeting', this.meetingName)
      this.meetingName = null
      this.$refs.meetingName.focus()
    }
  }
}
</script>
