<template>
  <div
    v-if="showGriddle"
    ref="griddle"
    class="griddle-container"
  >
    <div
      v-for="i in numberOfColumns"
      :key="i"
      class="griddle-column"
    />
  </div>
</template>

<script>
export default {
  data () {
    return {
      showGriddle: false,
      numberOfColumns: 0
    }
  },
  mounted () {
    window.addEventListener('keyup', (e) => {
      if (e.ctrlKey && e.shiftKey && e.which === 76) {
        this.showGriddle = !this.showGriddle
        this.$nextTick(() => {
          this.countColumns()
        })
      }
    })
  },
  methods: {
    countColumns () {
      if (Object.keys(this.$refs).length && this.$refs.griddle) {
        this.numberOfColumns = getComputedStyle(this.$refs.griddle).gridTemplateColumns.split(' ').length
      }
    }
  }
}
</script>
