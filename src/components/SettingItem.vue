<template>
  <v-row class="align-center">
    <v-col cols="4">
      <div>{{ subtitle }}</div>
    </v-col>
    <v-col cols="auto" class="d-flex ga-5 align-start">
      <slot></slot>
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: {
    subtitle: String,
  },
}
</script>
