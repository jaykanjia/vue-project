<script setup lang="ts">
import IconWorld from '@/components/icons/IconWorld.vue'
</script>

<template>
  <div class="empty-weather-content">
    <IconWorld width="256" height="256" />
    <span>Search by city to know about weather condition</span>
  </div>
</template>

<style scoped>
.empty-weather-content {
  display: grid;
  justify-items: center;
  opacity: 0.2;
}
</style>
