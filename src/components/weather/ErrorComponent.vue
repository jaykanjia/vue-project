<script setup lang="ts">
defineProps<{
  error: string
}>()
</script>

<template>
  <div class="error-container">
    <p>{{ error.charAt(0).toUpperCase() + error.slice(1) }}</p>
  </div>
</template>

<style scoped>
.error-container {
  display: flex;
  flex: 1;
  place-items: center;
}
.error-container > p {
  color: red;
}
</style>
