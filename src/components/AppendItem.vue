<template>
  <div @click="append"
    class="flex jc-center ai-center grow-1 shrink-1 basis-10 pointer hover-1"
  >
    <code>
      {{ val }}
    </code>
  </div>
</template>

<script>
export default {
  name: 'AppendItem',
  props: {
    val: {
      type: [String, Number]
    }
  },

  methods: {
    append() {
      this.$emit('append', this.val);
    }
  }
}
</script>

<style scoped>


</style>