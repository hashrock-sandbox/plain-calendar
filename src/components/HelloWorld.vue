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
    },
    selectionTop() {
      return this.selection.start < this.selection.end ? this.selection.start : this.selection.end
    },
    selectionBottom() {
      return this.selection.start < this.selection.end ? this.selection.end : this.selection.start
    }
  },
  methods: {
    onKeyDown(e){
      const cursor = new Date(this.selection.end)
      const isArrow = ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].includes(e.key)
      if(!isArrow) return
      if(e.key === "ArrowUp"){
        cursor.setDate(cursor.getDate() - 1)
      }else if(e.key === "ArrowDown"){
        cursor.setDate(cursor.getDate() + 1)
      }else if(e.key === "ArrowLeft"){
        cursor.setDate(cursor.getDate() - 1)
      }else if(e.key === "ArrowRight"){
        cursor.setDate(cursor.getDate() + 1)
      }
      if(e.shiftKey){
        this.selection.end = cursor
      }else{
        this.selection.start = cursor
        this.selection.end = cursor
      }
      e.preventDefault()
      const endEl = this.$refs["day-" + cursor.getTime()]
      if(endEl && endEl.length > 0 && endEl[0]){
        endEl[0].$el.scrollIntoView({
          block: "nearest",
        })
      }
    }
  },
  mounted() {
    document.addEventListener("keydown", this.onKeyDown)
  },
  beforeDestroy() {
    document.removeEventListener("keydown", this.onKeyDown)
  }
}
</script>

<template>
  <div>
    <!-- カレンダー作るぞ -->
    <div>
      <Day v-for="date in dates" :key="date" :date="date"
        :ref="'day-'+date.getTime()"
        :selected="selectionTop <= date && date <= selectionBottom"
        :isCursor="date.getTime() === selection.end.getTime()"
        :items="items.filter(i => i.date.getTime() === date.getTime())" />
    </div>
  </div>
</template>

<style scoped>

</style>
