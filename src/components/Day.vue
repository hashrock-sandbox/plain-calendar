<template>
  <div class="day" :class="{selected, isCursor}">
    <div class="day__label">{{ date.getDate() }}</div>
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
  color: rgba(54, 42, 157, 0.5);
}

.selected{
  background-color: rgb(213, 208, 255);
}
.isCursor{
  border: 1px solid rgb(100, 146, 255);
}

.day__item {
  background-color: #EEE;
  flex: 1;
}
</style>