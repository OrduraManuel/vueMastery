<template>
  <h1>Events for Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="btnContainer">
      <router-link
        :to="{name: 'EventList' , query:{ page: page - 1}}"
        rel="prev"
        class="btn"
        v-if="page != 1"
        >Previous page</router-link>
      <router-link
        :to="{name: 'EventList' , query:{ page: page + 1}}"
        rel="next"
        class="btn"
        v-if="hasNextPage"
        >Next page</router-link>
    </div>
  </div>
</template>

<script>
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import {watchEffect} from 'vue'

export default {
  name: 'EventList',
  props: ['page'],
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },
  created() {
    watchEffect( ()=>{
      this.events = null
      EventService.getEvents(2, this.page)
        .then(response => {
          this.events = response.data
          this.totalEvents = response.headers['x-total-count']
        })
        .catch(error => {
          console.log(error)
        })
    })
    },
    computed: {
      hasNextPage(){
        var totalPages = Math.ceil(this.totalEvents / 2)
        return this.page < totalPages
      }
    }
  }

</script>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.btnContainer{
  width:100%;
  display: flex;
  justify-content: space-around;
}
.btnContainer .btn{
  border-radius: 15px;
  padding: 5px 10px;
  text-decoration: none;
  background: purple;
  color: white;
}
</style>
