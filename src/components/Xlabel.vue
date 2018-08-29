<template>
  <g :transform="`translate(${left}, ${top})`">
    
  </g>
</template>

<script>
export default {
  name: 'Path',
  props: {
    left: Number,
    top: Number
  }
}
</script>

