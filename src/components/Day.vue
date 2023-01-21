<template>
  <div class="day" :class="{selected, isCursor}">
    <div class="day__label" :class="dayType">{{ date.getDate() }}</div>
    <div class="day__item" v-for="item in items">
      {{ item.note }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    date: Date,
    items: Array,
    selected: Boolean,
    isCursor: Boolean,
    editing: Boolean,
  },
  computed: {
    dayType() {
      const day = this.date.getDay()
      if (day === 0) {
        return "day__label--sunday"
      } else if (day === 6) {
        return "day__label--saturday"
      } else {
        return ""
      }
    },
  },
}

</script>

<style scoped>
.day {
  border-bottom: 1px solid rgba(0, 0, 0, 0.3);
  display: flex;
}


.day__label {
  padding: 0.5em;
  width: 2em;
  text-align: center;
  font-weight: 700;
  color: rgb(74, 74, 74);
}

.day__label--saturday{
  color: rgb(54, 42, 157);
}
.day__label--sunday{
  color: rgb(255, 0, 0);
}

.selected{
  background-color: rgb(226, 222, 255);
}
.isCursor{
  border: 1px solid rgb(30, 97, 255);
}

.day__item {
  background-color: #EEE;
  flex: 1;
}
</style>