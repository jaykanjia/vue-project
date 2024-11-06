<script lang="ts" setup>
const props = defineProps<{
  width?: string
  height?: string
}>()

// Removed the setup function as it's not needed in <script setup>
const width = props.width || '24' // Default value if not provided
const height = props.height || '24' // Default value if not provided
</script>

<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    :height="height"
    :width="width"
    viewBox="0 -960 960 960"
    fill="#e8eaed"
  >
    <path
      d="M480-100q-133 0-226.5-92T160-416q0-63 24.5-120.5T254-638l226-222 226 222q45 44 69.5 101.5T800-416q0 132-93.5 224T480-100ZM240-416h480q0-47-18-89.5T650-580L480-748 310-580q-34 32-52 74.5T240-416Z"
    />
  </svg>
</template>
