<template>
  <div class="container">
    <div class="section">
      <div v-for="contestant in orderedContestants" :key="contestant.id" class="box has-background-warning">
        <p class="has-text-right">
          <span class="tag is-medium">
            {{ contestant.name }}
            <span v-if="contestant.heartRate > 0">&nbsp;❤️</span>
            <span v-else>&nbsp;🤍</span>
          </span>
        </p>
        <h1 class="is-size-1 has-text-centered has-text-weight-semibold">{{ contestant.heartRate }}</h1>
        <h2 class="is-size-6 has-text-centered measured-at">{{ contestant.measuredAt?.toLocaleTimeString() }}</h2>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: function () {
    return {
      contestants: [
         {
          id: 1,
          name: 'Kenneth',
          token: '84a23eee-28ee-4946-9066-8291b8816d93',
          heartRate: 0,
          measuredAt: null
        },
        {
          id: 2,
          name: 'Rob',
          token: '0f0fd8f2-409e-4602-8c08-57503d9cf379',
          heartRate: 0,
          measuredAt: null
        },
        {
          id: 3,
          name: 'Joris',
          token: 'dummy-token',
          heartRate: 0,
          measuredAt: null
        }
      ],
    }
  },
  computed: {
    orderedContestants: function () {
      return [...this.contestants].sort((a, b) => b.heartRate - a.heartRate)
    }
  },
  mounted: function () {
    for (const contestant of this.contestants) {
      const url = `wss://dev.pulsoid.net/api/v1/data/real_time?access_token=${contestant.token}`
      const socket = new WebSocket(url)
      socket.onmessage = (event) => {
        const message = JSON.parse(event.data)
        contestant.heartRate = message.data.heart_rate
        contestant.measuredAt = new Date(message.measured_at)
      }
    }
  }
}
</script>

<style>
  @import 'https://cdn.jsdelivr.net/npm/bulma@0.9.3/css/bulma.min.css';
</style>
