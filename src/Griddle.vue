<template>
  <div
    v-if="showGriddle"
    ref="griddle"
    class="griddle"
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
      numberOfColumns: 12
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

<style lang="scss" scoped>
@import "@braid/griddle-scss/scss/griddle";

.griddle {
  @extend %griddle-template;
}

.griddle-column {
  background-color: rgba(red, 0.1);
}
</style>
