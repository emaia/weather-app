<template>
  <div class="text-white text-left shadow-lg">
    <div class="places-input text-gray-800">
      <input
        id="address"
        type="search"
        class="w-full py-2 px-3 shadow-sm rounded focus:bg-blue-200"
        placeholder="Type a city"
      />
      <div id="address-value" class="hidden"></div>
    </div>
    <div
      class="weather-container font-sans w-128 max-w-lg rounded-lg overflow-hidden bg-gray-900 shadow mt-4"
    >
      <div class="current-weather flex items-center justify-between px-6 py-8">
        <div class="flex items-center">
          <div>
            <div class="text-6xl font-bold">
              {{ currentTemperature.actual }}&deg;C
            </div>
            <div>Feels like {{ currentTemperature.feels }}&deg;C</div>
          </div>
          <div class="mx-5 text-left">
            <div class="font-bold">{{ currentTemperature.summary }}</div>
            <div>{{ location.name }}</div>
          </div>
        </div>
        <div>
          <client-only>
            <skycon :condition="currentTemperature.icon" />
          </client-only>
        </div>
      </div>
      <div class="future-weather text-sm bg-gray-800 px-4 py-2 overflow-hidden">
        <div
          v-for="day in fiveDays"
          :key="day.time"
          class="flex items-center mt-4"
        >
          <div class="w-1/6 text-lg pl-4 text-gray-200">
            {{ weekDay(day.time) }}
          </div>
          <div class="w-4/6 px-2 flex items-center">
            <div>
              <client-only>
                <skycon :condition="day.icon" :width="24" :height="24" />
              </client-only>
            </div>
            <div class="ml-3 text-left">{{ day.summary }}</div>
          </div>
          <div class="w-1/6 text-right pr-4">
            <div>{{ Math.round(day.temperatureHigh) }}&deg;C</div>
            <div>{{ Math.round(day.temperatureLow) }}&deg;C</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style>
/*  */
</style>
<script>
import { format } from 'date-fns'

let places
if (process.client) {
  places = require('places.js')
}

export default {
  data() {
    return {
      currentTemperature: {
        actual: '',
        feels: '',
        summary: '',
        icon: ''
      },
      daily: [],
      location: {
        name: 'São Paulo, Brasil',
        lat: '-23.5507',
        lng: '-46.6334'
      }
    }
  },
  computed: {
    fiveDays() {
      const fiveDays = this.daily.filter((day, index) => {
        return index <= 4
      })
      return fiveDays
    }
  },
  watch: {
    location: {
      handler(newValue, oldValue) {
        this.fetchData()
      },
      deep: true
    }
  },
  mounted() {
    this.fetchData()

    const placesAutocomplete = places({
      appId: process.env.ALGOLIA_APP_ID,
      apiKey: process.env.ALGOLIA_SEARCH_API_KEY,
      container: document.querySelector('#address')
    }).configure({
      type: 'city',
      aroundLatLngViaIp: false
    })

    const $address = document.querySelector('#address-value')
    placesAutocomplete.on('change', (e) => {
      $address.textContent = e.suggestion.value
      this.location.name = e.suggestion.value
      this.location.lat = e.suggestion.latlng.lat
      this.location.lng = e.suggestion.latlng.lng
    })

    placesAutocomplete.on('clear', () => {
      $address.textContent = 'none'
    })
  },
  methods: {
    weekDay(date) {
      const stamp = date * 1000
      return format(stamp, 'EEEEEE')
    },
    fetchData() {
      this.$axios
        .$get(`/api/weather`, {
          params: { lat: this.location.lat, lng: this.location.lng }
        })
        .then((res) => {
          const { currently, daily } = res.weather

          this.currentTemperature.actual = Math.round(currently.temperature)
          this.currentTemperature.feels = Math.round(
            currently.apparentTemperature
          )
          this.currentTemperature.summary = currently.summary
          this.currentTemperature.icon = currently.icon

          this.daily = daily.data
        })
    }
  }
}
</script>

<style>
.ap-footer svg {
  display: inline;
}
.ap-suggestion-icon svg {
  @apply inline-block;
  @apply -mt-3;
}
</style>
