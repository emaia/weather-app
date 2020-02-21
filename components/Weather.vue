<template>
  <div class="text-white">
    <div class="places-input text-gray-800">
      <input type="text" class="w-full" />
    </div>
    <div
      class="weather-container font-sans w-128 max-w-lg rounded-lg overflow-hidden bg-gray-900 shadow mt-4"
    >
      <div class="current-weather flex items-center justify-between px-6 py-8">
        <div class="flex items-center">
          <div>
            <div class="text-6xl font-semi-bold">8C</div>
            <div>Feels like 2c</div>
          </div>
          <div class="mx-5">
            <div class="font-semi-bold">Cloudy</div>
            <div>Toronto, Canada</div>
          </div>
        </div>
        <div>icon</div>
      </div>
      <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
        <div class="flex items-center mt-8">
          <div class="w-1/6 text-lg text-gray-200">MON</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>icon</div>
            <div class="ml-3">Cloudy with a chance of showers</div>
          </div>
          <div class="w-1/6 text-right">
            <div>5C</div>
            <div>-2C</div>
          </div>
        </div>
        <div class="flex items-center mt-8">
          <div class="w-1/6 text-lg text-gray-200">MON</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>icon</div>
            <div class="ml-3">Cloudy with a chance of showers</div>
          </div>
          <div class="w-1/6 text-right">
            <div>5C</div>
            <div>-2C</div>
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
export default {
  mounted() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      fetch(
        `https://cors-anywhere.herokuapp.com/https://api.darksky.net/forecast/29755197363e424086272d29b721a8d9/37.8267,-122.4233`
      )
        .then((res) => res.json())
        .then((data) => {
          console.log(data)
        })
    }
  }
}
</script>
