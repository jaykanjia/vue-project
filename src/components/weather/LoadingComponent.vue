<template>
  <div class="loading-container">
    <span class="loader"></span>
  </div>
</template>

<style scoped>
.loading-container {
  display: flex;
  flex: 1;
  justify-content: center;
  align-items: center;
}
.loader {
  width: 40px;
  height: 40px;
  border: 5px solid var(--color-primary);
  border-top: 5px solid transparent;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
