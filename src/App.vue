<script setup lang="ts">
import { ref, computed } from 'vue'
import axios from 'axios'
import Cloud from './components/icons/Cloud.vue'
import Pin from './components/icons/Pin.vue'
import Wind from './components/icons/Wind.vue'
import Dark from './components/icons/Dark.vue'
import Light from './components/icons/Light.vue'
import DefaultWeatherIcon from '/src/assets/icons/803-clouds-dn.svg'
import ThunderstormIcon from '/src/assets/icons/2xx-thunderstorm-dn.svg'
import DrizzleIcon from '/src/assets/icons/3xx-drizzle-dn.svg'
import RainNightIcon from '/src/assets/icons/5xx-rain-n.svg'
import RainDayIcon from '/src/assets/icons/5xx-rain-d.svg'
import ClearNightIcon from '/src/assets/icons/800-clear-n.svg'
import ClearDayIcon from '/src/assets/icons/800-clear-d.svg'
import Clouds801NightIcon from '/src/assets/icons/801-clouds-n.svg'
import Clouds801DayIcon from '/src/assets/icons/801-clouds-d.svg'
import Clouds802NightIcon from '/src/assets/icons/802-clouds-n.svg'
import Clouds802DayIcon from '/src/assets/icons/802-clouds-d.svg'
import Clouds803Icon from '/src/assets/icons/803-clouds-dn.svg'

const isDarkMode = ref<boolean>(isNightTime())

function isNightTime(): boolean {
  const hours = new Date().getHours()
  return hours > 17 || hours < 6
}

function applyTheme(): void {
  const html = document.querySelector('html') as HTMLHtmlElement
  if (isDarkMode.value) {
    html.classList.add('dark')
  } else {
    html.classList.remove('dark')
  }
}

const iconColor = computed<string>(() => {
  return isDarkMode.value ? '#FFFFFF' : '#296399'
})

function onChangeTheme(): void {
  isDarkMode.value = !isDarkMode.value
  applyTheme()
}

type LatLon = {
  lat: number
  lon: number
}

/* Bandung */
const defaultLocation: LatLon = {
  lat: -6.92138890001028,
  lon: 107.61117119810953,
}

function getLocation(): void {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(showPosition, console.error)
  } else {
    console.error('Geolocation is not supported by this browser.')
    fetchData(defaultLocation)
  }
}

interface GeoLocation {
  coords: {
    latitude: number
    longitude: number
  }
}

function showPosition(position: GeoLocation): void {
  const location = {
    lat: position.coords.latitude,
    lon: position.coords.longitude,
  }
  fetchData(location)
}

const API_KEY: string = '154dbbcee969ad6aabfee56eb06bd7df'

const BASE_URL: string = 'https://api.openweathermap.org/data/2.5/weather'

type Weather = {
  temperature: number
  weather: string
  location: string
  windSpeed: number
  cloud: number
  icon: string
}

const data = ref<Weather>({
  temperature: 0,
  weather: 'Loading...',
  location: 'Loading...',
  windSpeed: 0,
  cloud: 0,
  icon: DefaultWeatherIcon,
})

type Params = {
  appid: string
  lat: number
  lon: number
  units: string
}

function fetchData(location: LatLon) {
  const params: Params = {
    appid: API_KEY,
    lat: location.lat,
    lon: location.lon,
    units: 'metric',
  }

  return axios.get(BASE_URL, { params })
    .then((response) => {
      const weather = response.data
      const weatherId = weather.weather[0].id

      data.value = {
        temperature: parseInt(weather.main.temp),
        weather: getWeatherName(weatherId),
        location: weather.name,
        windSpeed: Math.round(weather.wind.speed * 3.6),
        cloud: weather.clouds.all,
        icon: getWeatherIcon(weatherId),
      }
    })
}

function getWeatherName(id: number): string {
  if (id === 800) return 'Clear'
  if (id >= 801) return 'Cloudy'
  if (id >= 701) return 'Atmosphere'
  if (id >= 600) return 'Snowy'
  if (id >= 500) return 'Rain'
  if (id >= 300) return 'Drizzle'
  if (id >= 200) return 'Thunderstorm'
  return 'Unknown'
}

function getWeatherIcon(id: number): string {
  switch (id) {
    case 200:
    case 201:
    case 202:
    case 210:
    case 211:
    case 212:
    case 221:
    case 230:
    case 231:
    case 232:
      return ThunderstormIcon
    case 300:
    case 301:
    case 302:
    case 310:
    case 311:
    case 312:
    case 313:
    case 314:
    case 321:
      return DrizzleIcon
    case 500:
    case 501:
    case 502:
    case 503:
    case 504:
    case 511:
    case 520:
    case 521:
    case 522:
    case 531:
      return isNightTime() ? RainNightIcon : RainDayIcon
    case 800:
      return isNightTime() ? ClearNightIcon : ClearDayIcon
    case 801:
      return isNightTime() ? Clouds801NightIcon : Clouds801DayIcon
    case 802:
      return isNightTime() ? Clouds802NightIcon : Clouds802DayIcon
    case 803:
    case 804:
      return Clouds803Icon
    default:
      return Clouds803Icon
  }
}

function onCreated(): void {
  applyTheme()
  getLocation()
}

onCreated()
</script>

<template>
  <div class="flex items-center justify-center h-screen">
    <div>
      <div class="card">
        <div class="grid grid-cols-2 gap-2 place-items-center">
          <div>
            <img class="h-32" :src="data.icon" alt="weather-icon" />
          </div>
          <div>
            <div class="text-7xl font-bold dark:text-slate-300">
              <span>{{ data.temperature }}</span>
              <span>°</span>
            </div>
            <div class="text-lg primary-text dark:text-[#6E85C1] mt-2">
              {{ data.weather }}
            </div>
          </div>
        </div>

        <div
          class="grid grid-cols-3 gap-3 place-items-center text-sm mt-10 mb-2"
          align="center"
        >
          <div>
            <div title="Wind">
              <Wind class="size-5 mb-3" :color="iconColor" />
            </div>
            <div class="caption dark:text-[#8497C9]">{{ data.windSpeed }} km/h</div>
          </div>
          <div>
            <div title="Location">
              <Pin class="size-5 mb-3" :color="iconColor" />
            </div>
            <div
              class="caption dark:text-[#8497C9] break-all location-text"
              :title="data.location"
            >
              {{ data.location }}
            </div>
          </div>
          <div>
            <div title="Clouds">
              <Cloud class="size-5 mb-3" :color="iconColor" />
            </div>
            <div class="caption dark:text-[#8497C9]">{{ data.cloud }}%</div>
          </div>
        </div>
      </div>

      <div
        class="m-5 text-center"
        :title="`${ isDarkMode ? 'Light' : 'Dark' } Mode`"
      >
        <button class="m-auto p-1 cursor-pointer" @click="onChangeTheme">
          <Light v-show="isDarkMode" />
          <Dark v-show="!isDarkMode" />
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
@reference '@/assets/styles/main.css';

.caption {
  font-family: 'DM Sans', sans-serif;
  font-optical-sizing: auto;
  font-weight: 500;
}

.card {
  @apply rounded-2xl px-4 py-10 m-5 drop-shadow-2xl w-80 bg-[#C9E5FF] dark:bg-[#1F2E54] transition-colors duration-300;
}

.location-text {
  width: 90px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
</style>
