<template>
  <v-row class="pa-5 mb-3">
    <v-col cols="3">
      <div v-if="title">
        <li>
          <strong>{{ title }}</strong>
        </li>
      </div>
    </v-col>
    <v-col>
      <slot></slot>
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: {
    title: String,
  },
}
</script>

<style scoped>
strong {
  font-size: 16px;
}
</style>
