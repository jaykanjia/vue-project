<script setup lang="ts">
import HelloWorld from '@/components/HelloWorld.vue'
import Header from '@/components/MainHeader.vue'
import WeatherCard from '@/components/weather/WeatherCard.vue'
</script>

<template>
  <Header>
    <template #image>
      <img
        alt="Vue logo"
        class="logo"
        src="./assets/logo.svg"
        width="125"
        height="125"
      />
    </template>
    <HelloWorld msg="Vue Weather App" />
  </Header>
  <main>
    <WeatherCard />
  </main>
</template>

<style scoped>
.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  .logo {
    margin: 0 2rem 0 0;
  }
}
</style>
