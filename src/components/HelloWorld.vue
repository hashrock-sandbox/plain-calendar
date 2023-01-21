<script>
import Day from "./Day.vue"

export default {
  props: {
    msg: String
  },
  components: {
    Day
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
        start: new Date("2023-01-01"),
        end: new Date("2023-02-28"),
      },
      selection: {
        start: new Date("2023-01-11"),
        end: new Date("2023-01-21"),
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
      <Day v-for="date in dates" :key="date" :date="date"
        :selected="selection.start <= date && date <= selection.end"
        :items="items.filter(i => i.date.getTime() === date.getTime())" />
    </div>
  </div>
</template>

<style scoped>
.day {
  border: 1px solid;
  display: flex;
}
</style>
