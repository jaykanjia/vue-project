<script setup lang="ts">
import { ref, watch } from 'vue'
import IconSearch from '@/components/icons/IconSearch.vue'
import type { CityGeocodingData, WeatherData } from '@/types/glogal'
import ErrorComponent from '@/components/weather/ErrorComponent.vue'
import LoadingComponent from '@/components/weather/LoadingComponent.vue'
import WeatherComponent from '@/components/weather/WeatherData.vue'
import EmptyComponent from '@/components/weather/EmptyComponent.vue'

const search = ref<string | null>(null)
const cities = ref<CityGeocodingData[] | null>(null)
const weatherData = ref<WeatherData | null>(null)
const error = ref<string | null>(null)
const isLoading = ref<boolean>(false)

const isMetric = ref<boolean>(false)

const debounceTimeout = ref<number | null>(null)

watch(search, (newValue, oldValue) => {
  if (oldValue?.toLowerCase() === newValue?.toLowerCase()) return

  if (!newValue || newValue.length < 3) return

  if (debounceTimeout.value) {
    clearTimeout(debounceTimeout.value) // Clear the previous timeout
  }

  debounceTimeout.value = window.setTimeout(async () => {
    // Use window.setTimeout
    console.log('Search value changed:', newValue) // Log after debounce
    const data = await fetchCityData(newValue)
    cities.value = data
  }, 300)
})

async function fetchWeatherData(
  city: string,
  isMetric: boolean,
): Promise<WeatherData> {
  console.log(
    `Fetching weather data for city: ${city}, units: ${isMetric ? 'metric' : 'standard'}`,
  )
  const apiUrl = import.meta.env.VITE_API_URL
  const apiKey = import.meta.env.VITE_API_KEY

  console.log({ apiUrl, apiKey })
  const response = await fetch(
    `${apiUrl}/data/2.5/weather?q=${city}&appid=${apiKey}&units=${isMetric ? 'metric' : 'standard'}`,
  )

  if (!response.ok) {
    const errorData = await response.json()

    const errorMessage = errorData.message || 'An unknown error occurred'
    throw new Error(errorMessage)
  }

  return await response.json()
}

async function fetchCityData(
  query: string,
  limit: number = 5,
): Promise<CityGeocodingData[]> {
  const apiUrl = import.meta.env.VITE_API_URL
  const apiKey = import.meta.env.VITE_API_KEY

  console.log({ apiUrl, apiKey })
  const response = await fetch(
    `${apiUrl}/geo/1.0/direct?q=${query},,&limit=${limit}&appid=${apiKey}`,
  )

  if (!response.ok) {
    const errorData = await response.json()

    const errorMessage = errorData.message || 'An unknown error occurred'
    throw new Error(errorMessage)
  }

  return await response.json()
}

async function handleSearch() {
  if (!search.value) return
  cities.value = null
  console.log('search', search.value)

  isLoading.value = true
  weatherData.value = null
  error.value = null
  try {
    const data = await fetchWeatherData(search.value, isMetric.value)
    console.log({ data })

    weatherData.value = data
  } catch (err: unknown) {
    error.value =
      err instanceof Error ? err.message : 'An unknown error occurred'
  } finally {
    isLoading.value = false
    cities.value = null
  }
}

async function toggleUnit() {
  isMetric.value = !isMetric.value
  await handleSearch()
}

async function handleCityChange(city: string) {
  search.value = city
  cities.value = null

  await handleSearch()
}
</script>

<template>
  <div class="weather-card no-scrollbar">
    <div class="form-container">
      <form @submit.prevent="handleSearch">
        <div class="search-container">
          <label aria-label="search" class="searchbar-wrapper">
            <input type="text" v-model="search" placeholder="Search..." />
            <ul v-if="cities" class="search-suggestions">
              <li
                v-for="city in cities"
                :key="`${city.name}`"
                @click="handleCityChange(city.name)"
              >
                {{ city.name }}
              </li>
            </ul>
          </label>
          <button type="submit" class="search-btn" aria-label="Search">
            <IconSearch />
          </button>
        </div>
      </form>
      <div class="toggle-container">
        <label class="toggle-switch">
          <!-- <input type="checkbox" v-model="isMetric" @checked="toggleUnit" /> -->
          <button
            class="hidden-btn"
            @click="toggleUnit"
            aria-label="toggle unit"
          ></button>
          <span :class="`slider ${isMetric && 'active'}`"></span>
        </label>
        <span>{{ isMetric ? 'Metric' : 'Standard' }}</span>
      </div>
    </div>
    <EmptyComponent v-if="weatherData === null" />
    <LoadingComponent v-if="isLoading" />
    <WeatherComponent
      v-if="weatherData"
      :weatherData="weatherData"
      :isMetric="isMetric"
    />
    <ErrorComponent v-if="error" :error="error" />
  </div>
</template>

<style scoped>
.weather-card {
  display: flex;
  flex-direction: column;
  height: 95dvh;
  max-height: 500px;
  max-width: 400px;
  margin-inline: auto;
  border: 1px solid var(--color-primary);
  border-radius: 40px;
  overflow-y: scroll;
}
.weather-card > * {
  padding: 20px 20px 30px 20px;
}
.form-container {
  z-index: 50;
  position: sticky;
  top: 0px;
  background: linear-gradient(
    to bottom,
    var(--color-background) 80%,
    transparent 100%
  );
}
.search-container {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 16px;
}
.searchbar-wrapper {
  position: relative;
  width: 100%;
}
.search-suggestions {
  position: absolute;
  bottom: 0;
  transform: translateY(105%);
  border-radius: 8px;
  left: 0;
  z-index: 50;
  width: 100%;
  background-color: var(--color-background);
  border: 1px solid var(--color-primary);
  list-style: none;
  padding: 8px;
  margin: 0;
}
.search-suggestions li {
  padding: 4px 8px;
  margin: 0;
}
.search-suggestions li:hover {
  background-color: hsla(160, 100%, 37%, 0.2);
  cursor: pointer;
}
input {
  width: 100%;
  border: 2px solid var(--color-border);
  background-color: var(--color-background);
  border-radius: 50px;
  padding: 10px 16px;
  font-size: 20px;
  transition: border-color 0.3s;
  color: var(--color-text);
}
input:focus {
  border-color: var(--color-primary);
  outline: none;
}
.search-btn {
  padding: 10px;
  border: 2px solid var(--color-border);
  border-radius: 50%;
  background-color: transparent;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background-color 0.3s;
  color: var(--color-primary);
}
.search-btn:focus {
  outline: none;
  border-color: var(--color-primary);
}
.search-btn:hover {
  background-color: var(--color-border-hover);
  border-color: var(--color-primary);
}
.toggle-container {
  display: flex;
  align-items: center;
  gap: 10px;
}

.toggle-switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
}

.toggle-switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: var(--color-border);
  transition: 0.4s;
  border-radius: 34px;
}

.slider:before {
  position: absolute;
  content: '';
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  transition: 0.4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: var(--color-primary);
}

input:checked + .slider:before {
  transform: translateX(26px);
}

.slider.active {
  background-color: var(--color-primary);
}

.active.slider:before {
  transform: translateX(26px);
}

.hidden-btn {
  visibility: hidden;
}

.weather-temp {
  font-size: 22px;
  font-weight: bold;
}
</style>
