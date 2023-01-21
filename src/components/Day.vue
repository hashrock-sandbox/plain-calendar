<template>
  <div class="day" :class="{'selected': selected}">
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
  },
}

</script>

<style scoped>
.day__label {
  padding: 0.5em;
  width: 2em;
  text-align: center;
  font-weight: 700;
}

.selected{
  background-color: rgb(177, 168, 255);
}

.day__item {
  background-color: #EEE;
  flex: 1;
}
</style>