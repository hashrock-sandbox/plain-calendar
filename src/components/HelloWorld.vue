<script>
export default {
  props: {
    msg: String
  },
  data() {
    return {
      items: [
        {
          date: new Date("2023-01-30"),
          note: "test",
        },
        {
          date: new Date("2023-01-31"),
          note: "test2",
        }
      ],
      range: {
        start: "2023-01-01",
        end: "2023-02-28",
      }
    }
  },
  computed: {
    dates() {
      const start = new Date(this.range.start)
      const end = new Date(this.range.end)
      const dates = []
      while (start <= end) {
        dates.push(new Date(start))
        start.setDate(start.getDate() + 1)
      }
      return dates
    }
  }
}
</script>

<template>
  <div class="card">
    <!-- カレンダー作るぞ -->
    <div>
      <div class="day" v-for="date in dates">
        <div class="day__label">{{ date.getDate() }}</div>
        <div class="day__item" v-for="item in items">
          <div v-if="item.date.getTime() === date.getTime()">
            {{ item.note }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.day {
  border: 1px solid;
  display: flex;
}

.day__label {
  padding: 0.5em;
  width: 2em;
  text-align: center;
  font-weight: 700;
}

.day__item {
  background-color: #EEE;
  flex: 1;
}
</style>
