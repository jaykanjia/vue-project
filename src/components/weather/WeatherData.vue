<script setup lang="ts">
import type { WeatherData } from '@/types/glogal'
import WeatherInfoCard from '@/components/weather/WeatherInfoCard.vue'
import IconHumidity from '@/components/icons/IconHumidity.vue'
import IconWind from '@/components/icons/IconWind.vue'

defineProps<{
  weatherData: WeatherData
  isMetric: boolean
}>()

function formatDateToDDMMYYYY(utcTimestamp: number | undefined): string | null {
  if (!utcTimestamp) return null

  const date = new Date(utcTimestamp * 1000) // Convert seconds to milliseconds
  const day = String(date.getUTCDate()).padStart(2, '0')
  const month = String(date.getUTCMonth() + 1).padStart(2, '0') // Months are zero-based
  const year = date.getUTCFullYear()

  return `${day}/${month}/${year}`
}
</script>

<template>
  <div class="weather-content">
    <div class="main-weather-info">
      <img
        alt="weather condition image"
        class="weather-img"
        :src="`https://openweathermap.org/img/wn/${weatherData?.weather[0]?.icon}@4x.png`"
      />
      <div>
        <h class="weather-heading">{{ weatherData?.name }}</h>
        <p class="weather-temp">
          {{ weatherData?.main?.temp }} {{ isMetric ? '°C' : '°F' }}
        </p>
        <p>{{ formatDateToDDMMYYYY(weatherData?.dt) }}</p>
      </div>
    </div>
    <div class="weather-info-container">
      <WeatherInfoCard>
        <template #icon><IconHumidity height="38" width="38" /></template>
        <template #title>Humidity</template>
        <span>{{ weatherData?.main?.humidity }}%</span>
      </WeatherInfoCard>
      <WeatherInfoCard>
        <template #icon><IconWind height="38" width="38" /></template>
        <template #title>Wind</template>
        <span>{{ weatherData?.wind?.speed }} m/s</span>
      </WeatherInfoCard>
    </div>
  </div>
</template>

<style scoped>
.weather-content {
  padding-top: 0px !important;
  display: grid;
  place-content: center;
  flex: 1;
  align-content: stretch;
}
.main-weather-info {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
  justify-content: space-between;
  align-items: center;
}
.main-weather-info > * {
  flex-basis: 50%;
}
.weather-heading {
  font-size: 32px;
  font-weight: bold;
}
.weather-img {
  width: 100%;
  aspect-ratio: 1/1;
  object-fit: contain;
}
.weather-info-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 0.5rem;
}
</style>
