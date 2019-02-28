<template>
<div class="allPage">
  <h1>Weather in your city</h1>
  <div class="inputBox">
    <input id="inputUser" type="text" name="" v-model="userInput" @keyup.enter="getIDs" placeholder="City...">
    <button for="inputUser" type="button" name="button" @click="getIDs">Search</button>
  </div>
  <div v-if="isLoaded" class="resultsContainer">
    <div v-if="!citysArr.length">
      <h2>Sorry, no results</h2>
    </div>
    <div class="cityBlock" v-for="city in citysArr" :key="city._v.id">
      <div class="weatherImg">
        <img :src="`http://openweathermap.org/img/w/${city._v.weather[0].icon}.png`" alt="Icon">
      </div>
      <div class="infoWeather">
        <p>{{ city._v.name }}, {{ city._v.sys.country  }} <img :src="`http://openweathermap.org/images/flags/${city._v.sys.country.toLowerCase()}.png`" alt="Flag"> <span class="temp"> {{ city._v.main.temp }} °С </span>{{ makeFirstUperCase(city._v.weather[0].description) }}</p>
        <p>Temperature from {{ city._v.main.temp_min }} to {{ city._v.main.temp_max }} °С, wind {{ city._v.wind.speed }} m/s. clouds {{ city._v.clouds.all }} %, {{ city._v.main.humidity }} % humidity</p>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import file from './assets/current.city.list.min.json'

export default {
  name: 'app',
  data() {
    return {
      userInput: "",
      citysArr: [],
      isLoaded: false
    }
  },
  methods: {
    async getData(id) {
      //await the response of the fetch call
      let response = await fetch(`http://api.openweathermap.org/data/2.5/weather?id=${id}&units=metric&appid=01b8ee546b2eb4b5ccb158c48f2db08c`);
      //proceed once the first promise is resolved.
      let data = await response.json()
      //proceed only when the second promise is resolved
      window.console.log("fecth complete")
      return data;
    },


    makeFirstUperCase(str) {
      return `${str[0].toUpperCase()}${str.substring(1)}`
    },

    async getIDs() {
      this.isLoaded = false;
      this.citysArr = file.filter(city => city.name.toLowerCase().includes(this.userInput.toLowerCase())).map(city => city.id).flatMap(id => this.getData(id));
      await Promise.all(this.citysArr)
      window.console.log(this.citysArr);
      this.isLoaded = true;
    }
  },
  created() {
    window.console.log(file);
  },

  updated() {
    // this.isLoaded = true;
  }
}
</script>
