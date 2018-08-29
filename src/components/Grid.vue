<template>
  <g name="grid">
    <line :x1="x1" :y1="y1" :x2="x2" :y2="y1" name="top" class="border"></line>
    <line :x1="x2" :y1="y1" :x2="x2" :y2="y2" name="right" class="border"></line>
    <line :x1="x1" :y1="y2" :x2="x2" :y2="y2" name="bottom" class="border"></line>
    <line :x1="x1" :y1="y2" :x2="x1" :y2="y1" name="left" class="border"></line>
    <g :transform="`translate(${grid.left}, ${grid.top})`">
      <slot></slot>
    </g>
  </g>
</template>

<script>
export default {
  name: 'Grid',
  props: {
    grid: Object,
  },
  computed: {
    x1: function() {
      return this.grid.left
    },
    x2: function() {
      return this.grid.left + this.grid.width
    },
    y1: function() {
      return this.grid.top
    },
    y2: function() {
      return this.grid.top + this.grid.height
    },
  }
}
</script>

<style scoped>
.border {
  stroke: #394457;
  stroke-width: 1;
  fill: transparent;
}
</style>


