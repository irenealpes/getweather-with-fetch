<template>
  <div>
    <h1 class="m-8 text-gray-400 tracking-wide hover:text-indigo-600 text-[50px]">Tell me the weather!</h1>
    <input v-model="location" class="border p-2 ml-8 m-5" type="text">
    <button class="border p-3 rounded-lg bg-orange-200" @click="getWeather">Get weather</button>

    <div class="flex px-3 m-5">
      <input type="text" id="website-admin" class="outline-none rounded-none rounded-l-lg bg-gray-50 border text-gray-900 focus:ring-blue-500 focus:border-blue-500 block flex-1 min-w-0 w-full text-sm border-gray-300 p-2.5  dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="Own loction weather">
        <button @click="getWeatherByLocation" class="inline-flex items-center px-3 text-sm text-gray-900 bg-gray-200 border border-l-0 border-gray-300 rounded-r-md dark:bg-gray-600 dark:text-gray-400 dark:border-gray-600">
            #
        </button>
    </div>

  
    <div v-if="loading">
      <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-black" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
      </svg>
    </div>

    <div v-if="error" class="text-3xl text-red-500 px-8">
      {{error}}
    </div>

    <div v-if="weather" class="m-5 bg-gradient-to-br from-cyan-400 to-slate-100 rounded-lg p-6 shadow-lg">
      <h2 class="text-white text-2xl shadow-lg">{{weather.name}}</h2>
      <img :src="iconWeather">
      <h1 class="p-3 text-2xl bg-white rounded-lg shadow-lg">{{weather.main.temp}}ºC</h1>
      <p class="px-3 pt-3 text-slate-500">Humidity: {{weather.main.humidity}}%</p>
      <p class="px-3 text-slate-500">{{weather.weather[0].main}}</p>
      <p class="px-3 text-slate-500">{{weather.weather[0].description}}</p>
    </div>

    <ListDays v-if="weatherFiveDays" :weatherFiveDays="weatherFiveDays"/>


    <p class="m-8 font-light text-[15px]">ⓒ Developed by Irene Sanchez.</p>


  </div>  
</template>

<script>
import ListDays from './components/ListDays.vue';
export default {
  name: "App",
  data() {
    return {
      loading: false,
      location: "",
      weather: null,
      error: null,
      apiKey:'86defffd5ce02cc4ca3d4f744ee8d29a',
      coordinates: null,
      weatherFiveDays: null
    };
  },
  components: {
    ListDays,
    ListDays
},
  computed:{
    iconWeather(){
      return `http://openweathermap.org/img/wn/${this.weather.weather[0].icon}@2x.png`
    }
  },
  methods: {
    getWeather() {
      this.loading = true;
      this.error = null;
      fetch (`https://api.openweathermap.org/data/2.5/weather?q=${this.location}&APPID=${this.apiKey}&units=metric`)
      .then(response => {
        if(!response.ok){
          throw new Error('City not found!')
        }
        return response.json()
      })
      .then(data => this.weather = data)
      .catch(error => {
        return this.error = error
      })
      .finally(() => {
        return this.loading = false
      }) 
    },

    
//esto siguiente da el mismo resultado que lo anterior pero es mas lio, mas desordenado. con los awaits nos aseguramos que todo pase al mismo tiempo
    //getWeatherByCoords(){
      //fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${this.location}&limit=1&appid=${this.apiKey}`)
      //.then(response => response.json())
      //.then(data => {
        //this.coordinates.lat = data[0].lat
        //this.coordinates.lon = data[0].lon
        //fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${this.coordinates.lat}&lon=${this.coordinates.lon}&appid=${this.apiKey}`)
        //.then(response => response.json())
        //.then(data => console.log(data))
      //})
    //}

    getWeatherByLocation(){
      if(navigator.geolocation) {
        this.loading = true;
        navigator.geolocation.getCurrentPosition(async (position) => {
          try {
            const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${position.coords.latitude}&lon=${position.coords.longitude}&appid=${this.apiKey}&units=metric`)
            if(!response.ok){
              throw new Error('City not found')
            }
            const data = await response.json();
            this.weather = data;
            this.location = data.name;
          }
          catch(error){
            console.log('Error result');
            return this.error = error
          }
          finally{
            return this.loading = false
          }
        });
      } else {
        console.log('Geolocation is not supported by this browser')
      }
    },   


  },
}
</script>

<style></style>
