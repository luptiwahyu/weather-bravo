<script setup lang="ts">
import { ref, computed } from 'vue'
import Cloud from './components/icons/Cloud.vue'
import Pin from './components/icons/Pin.vue'
import Wind from './components/icons/Wind.vue'
import Dark from './components/icons/Dark.vue'
import Light from './components/icons/Light.vue'

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

function onCreated(): void {
  applyTheme()
}

onCreated()
</script>

<template>
  <div class="flex items-center justify-center h-screen">
    <div>
      <div class="card">
        <div class="grid grid-cols-2 gap-2 place-items-center">
          <div>
            <img class="h-32" src="/src/assets/icons/803-clouds-dn.svg" alt="weather-icon" />
          </div>
          <div>
            <div class="text-7xl font-bold dark:text-slate-300">
              <span>{{ 30 }}</span>
              <span>°</span>
            </div>
            <div class="text-lg primary-text dark:text-[#6E85C1] mt-2">{{ 'Cloudy' }}</div>
          </div>
        </div>

        <div class="grid grid-cols-3 gap-3 place-items-center text-sm mt-10 mb-2" align="center">
          <div>
            <div title="Wind"><Wind class="size-5 mb-3" :color="iconColor" /></div>
            <div class="caption dark:text-[#8497C9]">{{ 50 }} km/h</div>
          </div>
          <div>
            <div title="Location"><Pin class="size-5 mb-3" :color="iconColor" /></div>
            <div
              class="caption dark:text-[#8497C9] break-all location-text"
              :title="'Bandung'"
            >
              {{ 'Bandung' }}
            </div>
          </div>
          <div>
            <div title="Clouds"><Cloud class="size-5 mb-3" :color="iconColor" /></div>
            <div class="caption dark:text-[#8497C9]">{{ 45 }}%</div>
          </div>
        </div>
      </div>

      <div class="m-5 text-center" :title="`${ isDarkMode ? 'Light' : 'Dark' } Mode`">
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
</style>
