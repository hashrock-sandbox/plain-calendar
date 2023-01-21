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
          note: "今日は楽しかったよ",
        },
        {
          date: new Date("2023-01-31"),
          note: "daiizさんと時間UIについて話す",
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
    },
    cursorElement(){
      return this.$refs["day-" + this.selection.end.getTime()]
    },
    cursorElementPosition(){
      const el = this.cursorElement
      if (el && el.length > 0 && el[0]) {
        const rect = el[0].$el.getBoundingClientRect()
        return {
          top: rect.top + window.scrollY,
          left: rect.left + window.scrollX,
          width: rect.width,
          height: rect.height,
        }
      }
      return null
    },
    editorStyle() {
      const pos = this.cursorElementPosition
      if (pos) {
        return {
          top: pos.top + "px",
          left: pos.left + 16 * 3 + "px",
        }
      }
      return {}
    }
  },
  methods: {
    onKeyDown(e) {
      const cursor = new Date(this.selection.end)
      const isArrow = ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].includes(e.key)
      if (!isArrow) return
      if (e.key === "ArrowUp") {
        cursor.setDate(cursor.getDate() - 1)
      } else if (e.key === "ArrowDown") {
        cursor.setDate(cursor.getDate() + 1)
      } else if (e.key === "ArrowLeft") {
        cursor.setDate(cursor.getDate() - 1)
      } else if (e.key === "ArrowRight") {
        cursor.setDate(cursor.getDate() + 1)
      }
      if (e.shiftKey) {
        this.selection.end = cursor
      } else {
        this.selection.start = cursor
        this.selection.end = cursor
      }
      e.preventDefault()
      const endEl = this.cursorElement
      if (endEl && endEl.length > 0 && endEl[0]) {
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
    <div class="wrapper">
      <Day v-for="date in dates" :key="date" :date="date" :ref="'day-' + date.getTime()"
        :selected="selectionTop <= date && date <= selectionBottom"
        :isCursor="date.getTime() === selection.end.getTime()"
        :items="items.filter(i => i.date.getTime() === date.getTime())" />
    </div>

    <div class="editor" :style="editorStyle">
      <input />
    </div>
  </div>
</template>

<style scoped>
.wrapper{
  position: relative;
}
.editor{
  position: absolute;
}
.editor input{
  height: 36px;
  border: none;
  font-size: inherit;
  font-family: inherit;
}

</style>
