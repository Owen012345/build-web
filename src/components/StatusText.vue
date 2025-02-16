<template>
  <p :class="status ? 'text-success mt-2 text-caption' : 'text-error mt-2 text-caption'">
    {{ text }}
  </p>
</template>

<script>
export default {
  props: {
    status: Boolean,
    text: String,
  },
}
</script>

<style scoped>
.text-success {
  color: #4caf50; /* 초록색 */
}
.text-error {
  color: #f44336; /* 빨간색 */
}
</style>
