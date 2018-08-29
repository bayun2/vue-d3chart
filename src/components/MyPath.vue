<template>
  <path :style="styleObject" :d="d"></path>
</template>

<script>
export default {
  name: 'MyPath',
  props: {
    styleObject: {
      default: () => ({
        type: Object,
        fill: 'none',
        stroke: '#318BEB',
        strokeWidth: '1px',
      })
    },
    d: String
  }
}
</script>

