<template>
  <v-app>
    <v-main>
      <v-container class="d-flex flex-column align-center justify-center">
        <v-text-field
          v-model="query"
          label="Search for a location"
          @keydown.enter="fetchWeather"
          outlined
          clearable
          class="search-bar mb-4"
        />

        <v-card v-if="weather.main" class="pa-4 weather-wrap" elevation="3">
          <v-card-title class="text-h5 text-center location-box">
            {{ weather.name || 'Unknown Location' }}, {{ weather.sys?.country || 'N/A' }}
          </v-card-title>
          <v-card-subtitle class="text-center mb-4">
            {{ currentDate }}
          </v-card-subtitle>
          <v-card-text class="text-center weather-box">
            <div class="temp text-h1">{{ Math.round(weather.main?.temp || 0) }}°C</div>
            <div class="weather text-h5 font-italic">{{ weather.weather[0]?.main || 'Unknown' }}</div>
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useFetch } from '@vueuse/core';

  name: 'App'
 
    const api_key = '326cf205c21a5cacd5310f96fa20f24c';
    const url_base = 'https://api.openweathermap.org/data/2.5/';
    const query = ref('');
    const weather = ref({
      main: null,
      sys: { country: '' },
      name: '',
      weather: [{ main: '' }],
    });

    const currentDate = computed(() => {
      const options = { weekday: 'long', month: 'short', day: 'numeric', year: 'numeric' };
      return new Date().toLocaleDateString('en-GB', options);
    });

    const fetchWeather = async () => {
      try {
        const { data, error } = await useFetch(
          `${url_base}weather?q=${query.value}&units=metric&APPID=${api_key}`
        ).json();

        if (error.value || !data.value || !data.value.main) {
          console.error('Error fetching weather:', error.value || 'Invalid response');
          resetWeather();
        } else {
          weather.value = data.value;
        }
      } catch (err) {
        console.error('Fetch error:', err);
        resetWeather();
      }
    };

    const resetWeather = () => {
      weather.value = {
        main: null,
        sys: { country: '' },
        name: '',
        weather: [{ main: '' }],
      };
    };

    
</script>

<style scoped>
.search-bar {
  max-width: 400px;
  width: 100%;
}
.weather-wrap {
  max-width: 400px;
  width: 100%;
}
.temp {
  color: #313131;
}
.location-box {
  color: #313131;
}
.weather {
  color: #313131;
}
</style>

