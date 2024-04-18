<template>
<div class="search-container">
    <input
      v-model="cityName"
      type="text"
      placeholder="Simple Weather"
      @keyup.enter="searchWeather"
    />
    <button class="searchbtn" @click="searchWeather"><span class="glyphicon glyphicon-search"></span></button>

    <div v-if="loading">
      <p>Loading weather data...</p>
    </div>

    <div class="weather-container" v-else-if="weatherData">
      <h2>Weather for {{ cityName }}</h2>
    
        <div class="hours-card">
            <h3>Next hours</h3>
            <div class="hours-data">
                <div class="hours-details" v-for="(hour, index) in weatherData.hourly.slice(1, 5)" :key="index">
                    <span class="temp"> {{ hour.temp }} ° </span>
                      <br>
                     <span class="humidity">{{ hour.humidity }} </span>
                     <br>
                     <img :src="getIconUrl(hour.weather[0]?.icon)" alt="weather icon" />
                     <br>
                     {{ new Date(hour.dt * 1000).toLocaleTimeString("en-US", { hour: "2-digit", minute: "2-digit"}) }}
                </div>
            </div>
        </div>

        <div class="days-card">
           <h3>Next 5 days</h3>
            <div class="days-data" >
                <div class="days-details" v-for="(day, index) in weatherData.daily.slice(0, 5)" :key="index">
                    <img :src="getIconUrl(day.weather[0]?.icon)" alt="weather icon" />
                    <!-- {{ day.weather[0].icon }} -->
                    <div class="weather-details"> 
                        
                        <p class="weather-days">{{ new Date(day.dt * 1000).toLocaleDateString("en-US", { weekday: 'short', month: 'short', day: 'numeric' }) }}</p>
                        <p class="weather-desc"> {{ day.weather[0]?.description }}. </p>
                    </div>
                    <span class="temp">  {{ day.temp.day }} ° </span>
                </div>
            </div>
       </div>
    </div>

    <div v-else-if="!weatherData && cityName">
      <p>No data available for the searched city.</p>
    </div>
</div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      cityName: '', 
      weatherData: null, 
      loading: false, 
    };
  },
  methods: {
    async searchWeather() {
      if (!this.cityName) {
        return; 
      }

      this.loading = true;
      try {
        const apiKey = '482944e26d320a80bd5e4f23b3de7d1f'; 
        const geoUrl = `https://api.openweathermap.org/data/2.5/weather?q=${this.cityName}&appid=${apiKey}`;

        const geoResponse = await axios.get(geoUrl);
        const { lat, lon } = geoResponse.data.coord;

        const weatherUrl = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=minutely&units=metric&appid=${apiKey}`;

        const weatherResponse = await axios.get(weatherUrl);
        this.weatherData = weatherResponse.data;
      } catch (error) {
        console.error('Error fetching weather data:', error);
        this.weatherData = null;
      } finally {
        this.loading = false; 
      }
    },
    getIconUrl(iconCode) {
      if (!iconCode) {
        return ''; 
      }
      return `https://openweathermap.org/img/wn/${iconCode}@2x.png`;
    },
  },
};
</script>

<style scoped>

input[type="text"] {
    padding: 20px;
    width: 100%;
    border: none;
    background: #1365c0;
    text-align: left;
    font-size: 30px;
}
input[type="text"]::placeholder {
  color: white;
  opacity: 1; 
}
.searchbtn{
    position: absolute;
    float: right;
    right: 10px;
    top: 3%;
    font-size: 2.5rem;
    cursor: pointer;
    padding: 10px;
    background: transparent;
    color: white;
    border: none;
}
.weather-container {
    background-color: #0690b9;
}
.weather-details {width: 40%;}
.humidity,
.temp,
.weather-days {
    color: #2cb5da;
    font-size: 16px;
    font-weight: 600;
    padding-top: 10px;
}
.temp, .weather-days {color: black;}
.weather-desc {
    color: #9c9c9c;
    font-weight: 600;
    font-size: 16px;
}
.weather-container h2 {
    text-align: center;
    padding-top: 5px;
    color: white;
    font-size: 20px;
    font-weight: 600;
}
.hours-card,
.days-card {
    background: white;
    display: flex;
    flex-direction: column;
    padding: 5px;
    margin: 15px;
    border-radius: 5px;
}
.hours-data {
    display: grid;
    grid-template-columns: repeat(4,1fr);
}
.hours-card h3,
.days-card h3 {
    border-bottom: 0.5px solid lightgray;
    padding: 5px;
    color: black;
    font-weight: 400;
    font-size: 24px;
}
.hours-details, 
.days-details {
    border-right: 0.5px solid lightgray;
    padding: 0;
    margin: 10px 4px;
    text-align: center;
}
.days-details {
    border-right: none;
    border-bottom:0.5px solid lightgray;
    display: flex;
    justify-content: space-around;
    align-items: center;
    padding: 0;
    margin: 0;
}
.hours-details img,
.days-details img {
    height: 80px;
    width: 80px;
}
</style>
