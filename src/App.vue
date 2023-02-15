<template>

  <div class="first w-80 h-56 rounded-lg shadow ml-2 mr-2 bg-[snow] flex flex-col justify-around items-center border-2 border-[#18122B]">
    <div class="first-header w-full h-12 -center border-b-2 border-[#18122B]">
      <p class="text-[#18122B] font-bold"> Weather App </p>
    </div>
    <div class="fixed-btm w-full h-[176px] flex justify-evenly items-center flex-col">
      <div class="first-input w-72 h-12 -center relative">
        <input type="text" class="w-full h-full rounded text-center outline-none text-[#18122B] font-bold border-2 border-[#18122B]" v-model="city" maxlength="19">
        <button class="w-8 h-8 rounded shadow bg-[#18122B] -center absolute right-0 mr-2.5" @click="getWeather"> <i class="fa-solid fa-caret-right text-[snow]"></i> </button>
      </div>
      <div class="first-divider w-72 h-2 flex justify-between items-end">
        <div class="40p w-2/5 h-1 border-t-2 border-[#18122B]"></div>
        <div class="20p -center w-[20%] h-full"> <p class="text-[#18122B] font-bold"> or </p> </div>
        <div class="40p w-2/5 h-1 border-t-2 border-[#18122B]"></div>
      </div>
      <div class="first-btn w-72 h-12 -center">
        <button class="w-full h-full rounded text-center outline-none text-[snow] bg-[#18122B]" @click="gWLocation"> Get Device Location </button>
      </div>
    </div>
  </div>

  <div class="second w-80 h-96 rounded-lg shadow ml-2 mr-2 bg-[snow] -center flex-col border-2 border-[#18122B]">
    <div class="top w-full h-12 flex justify-start items-center border-b-2 border-[#18122B]"> 
      <i class="fa-solid fa-arrow-left ml-4 text-[#18122B]"></i>
      <p class="ml-2 font-bold text-[#18122B]"> Weather App </p>
    </div>
    <div class="mid -center flex-col w-full h-[274px]"> 
      <p class="text-6xl font-bold" v-if="weatherData"> {{ weatherData.currentConditions.temp }}°C </p>
      <p class="text-6xl font-bold" v-if="!weatherData"> 0°C </p>
      <p class="text-xl font-bold mt-4" v-if="weatherData"> {{ weatherData.currentConditions.conditions }} </p>
      <p class="text-xl font-bold mt-4" v-if="!weatherData"> No condition yet </p>
      <p class="text-lg font-bold mt-4" v-if="weatherData"> <i class="fa-solid fa-location-dot"></i> {{ weatherData.resolvedAddress }} </p>
      <p class="text-lg font-bold mt-4" v-if="!weatherData"> <i class="fa-solid fa-location-dot"></i> Weather address </p>
    </div>
    <div class="bot -center w-full h-[62px]">
      <div class="first-mid w-2/4 h-full -center border-r border-t-2 border-[#18122B]">
        <div class="first-mid-icon -center">
          <i class="fa-solid fa-temperature-quarter text-3xl"></i>
        </div> 
        <div class="fixed-first-mid ml-2">
          <div class="first-mid-text-top">
            <p class="font-bold" v-if="weatherData"> {{ weatherData.currentConditions.feelslike }} °C </p>
            <p class="font-bold" v-if="!weatherData"> 0°C </p>
          </div>
          <div class="first-mid-text-top">
            <p class="font-bold text-xs"> Feels Like </p>
          </div>
        </div>
      </div>
      <div class="second-mid w-2/4 h-full -center border-l border-t-2 border-[#18122B]">
          <div class="second-mid-icon -center">
           <i class="fa-solid fa-droplet text-3xl"></i>
          </div> 
          <div class="fixed-second-mid ml-2">
            <div class="second-mid-text-top">
              <p class="font-bold" v-if="weatherData"> {{ weatherData.currentConditions.humidity }}% </p>
              <p class="font-bold" v-if="!weatherData"> 0% </p>
            </div>
            <div class="second-mid-text-top">
              <p class="font-bold text-xs"> humidity </p>
            </div>
          </div>
      </div>
    </div>
  </div>

</template>

<script>

import { ref } from '@vue/reactivity'
import { onUpdated } from '@vue/runtime-core'

export default {
  name: 'App',
  components: {
  },
  setup(props) {

    const city = ref("Enter city name")
    const weatherData = ref(null)
    const lanLonToCity = ref(null)
    let lan = ""
    let lon = ''

    function getWeather(set) {
      if (typeof(set) == "string") {
        fetch("https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/" + set.toLowerCase() + "?unitGroup=metric&key=XLTYBRXUUFQBZ5W6W7FDLB6AM&contentType=json").then((response) => response.json())
        .then((data) => weatherData.value = data);
      }
      fetch("https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/" + city.value + "?unitGroup=metric&key=XLTYBRXUUFQBZ5W6W7FDLB6AM&contentType=json").then((response) => response.json())
      .then((data) => weatherData.value = data);
    }

    function showPosition(position) {
      lan = position.coords.latitude 
      lon = position.coords.longitude;
      fetch("https://api.opencagedata.com/geocode/v1/json?q=" + lan +"," + lon + "&key=3d6b4c62b245439291c6783ee084d676&language=en&pretty=1")
      .then((response) => response.json())
      .then((data) => getWeather(data.results[0].components.state));
    }

    function getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition);
      } else { 
        console.log("Geolocation is not supported by this browser.")
      }
    }

    function gWLocation() {
      getLocation()
    }

    return { city, weatherData, getWeather, showPosition, gWLocation }
  }
}
</script>

<style>
* {
  color: #18122B;
  font-family: 'Nunito', sans-serif;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
