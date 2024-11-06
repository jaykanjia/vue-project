<template>
  <div class="info-card">
    <slot name="icon"></slot>
    <div class="info">
      <h3><slot name="title"></slot></h3>
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
.info-card {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  background-color: var(--color-border);
  border-radius: 8px;
  padding: 1rem;
}

.info {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.info > h3 {
  font-weight: bold;
}
</style>
