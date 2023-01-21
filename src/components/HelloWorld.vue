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
          note: "今日はGUIを作った",
        },
        {
          date: new Date("2023-01-31"),
          note: "自動勉強会予定日",
          range: {
            start: new Date("2023-01-31"),
            end: new Date("2023-02-02"),
          }
        }
      ],
      range: {
        start: new Date("2023-01-01"),
        end: new Date("2023-02-28"),
      },
      selection: {
        start: new Date("2023-01-11"),
        end: new Date("2023-01-21"),
      },
      editorContents : "",
      editing: false,
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
          top: rect.top + window.scrollY - 8, // FIXME
          left: rect.left + window.scrollX - 8, // FIXME
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
          opacity: this.editing ? 1 : 0,
        }
      }
      return {
        opacity: this.editing ? 1 : 0,
      }
    },
    cursorItem(){
      return this.items.find(i => i.date.getTime() === this.selection.end.getTime())
    },
    arrows(){
      return this.items.filter(i => i.range).map(i => {
        const start = new Date(i.range.start)
        const end = new Date(i.range.end)

        const rowHeight = 41

        const dayOffsetStart = (start.getTime() - this.range.start.getTime()) / (1000 * 60 * 60 * 24)
        const dayOffsetEnd = (end.getTime() - this.range.start.getTime()) / (1000 * 60 * 60 * 24)

        return {
          start: dayOffsetStart * rowHeight + rowHeight/2,
          end: dayOffsetEnd * rowHeight+ rowHeight/2,
        }
      })
    }
  },
  methods: {
    onKeyDown(e) {
      if (e.key === "Delete") {
        this.items = this.items.filter(i => i.date.getTime() !== this.selection.end.getTime())
        return
      }

      if (e.key === "Enter") {
        if (this.editing) {
          const item = this.cursorItem
          if (item) {
            item.note = this.editorContents
          } else {
            const range = this.selection.start.getTime() !== this.selection.end.getTime() ? {
              start: this.selection.start,
              end: this.selection.end,
            } : null

            this.items.push({
              date: new Date(this.selection.end),
              note: this.editorContents,
              range,
            })
          }
          if(this.editorContents === ""){
            this.items = this.items.filter(i => i.date.getTime() !== this.selection.end.getTime())
          }
          this.editing = false
        } else {
          this.editorContents = this.cursorItem ? this.cursorItem.note : ""
          this.editing = true
        }
        return
      }

      const cursor = new Date(this.selection.end)
      const isArrow = ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].includes(e.key)
      if (!isArrow) {
        this.editing = true
        return
      }
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
      this.editorContents = this.cursorItem ? this.cursorItem.note : ""
      e.preventDefault()
      const endEl = this.cursorElement
      if(this.$refs.input) {
        this.$refs.input.focus()
      }

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
  <div style="position: relative">
    <!-- カレンダー作るぞ -->
    <div class="wrapper">
      <Day v-for="date in dates" :key="date" :date="date" :ref="'day-' + date.getTime()"
        :selected="selectionTop <= date && date <= selectionBottom"
        :isCursor="date.getTime() === selection.end.getTime()"
        :items="items.filter(i => i.date.getTime() === date.getTime())" />
    </div>

    <div class="editor" :style="editorStyle">
      <input ref="input" v-model="editorContents" />
    </div>

    <svg ref="overlay" class="overlay">
      <!-- <rect top="0" left="0" width="100%" height="100%" fill="rgba(0,0,0,0.5)" /> -->
      <g v-for="arrow in arrows">
        <circle :cx="500" :cy="arrow.start" r="5" fill="rgba(10, 10, 200, 0.5)" />
        <line :y1="arrow.start" x1="500" :y2="arrow.end" x2="500" stroke="rgba(10, 10, 200, 0.5)" stroke-width="2" />
        <!-- arrow top -->
        <g v-if="arrow.start < arrow.end">
          <line :y1="arrow.end" x1="500" :y2="arrow.end - 10" x2="495" stroke="rgba(10, 10, 200, 0.5)" stroke-width="2" />
          <line :y1="arrow.end" x1="500" :y2="arrow.end - 10" x2="505" stroke="rgba(10, 10, 200, 0.5)" stroke-width="2" />
        </g>
        <!-- arrow bottom -->
        <g v-else>
          <line :y1="arrow.end" x1="500" :y2="arrow.end + 10" x2="495" stroke="rgba(10, 10, 200, 0.5)" stroke-width="2" />
          <line :y1="arrow.end" x1="500" :y2="arrow.end + 10" x2="505" stroke="rgba(10, 10, 200, 0.5)" stroke-width="2" />
        </g>

      </g>
    </svg>
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
  width: 24em;
  border: none;
  font-size: inherit;
  font-family: inherit;
}

.overlay{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}
</style>
