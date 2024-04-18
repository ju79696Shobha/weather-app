
<template>
  <div class="weather-container">
    <h2>Weather for {{ city.name }}</h2>
    <div v-if="loading">
      <p>Loading weather data...</p>
    </div>
    <div v-else>
        <div class="hours-card">
            <h3>Next hours</h3>
            <div class="hours-data">
                <div class="hours-details" v-for="(hour, index) in weatherData.hourly.slice(1, 5)" :key="index">
                    <span class="temp"> {{ hour.temp }}° </span>
                      <br>
                     <span class="humidity">{{ hour.humidity }}</span>
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
                        <p class="weather-desc"> {{ day.weather[0].description }}. </p>
                    </div>
                    <span class="temp">  {{ day.temp.day }} ° </span>
                </div>
            </div>
       </div>
        <button class="refreshbtn" @click="refreshData">Refresh Data</button>
    </div>
</div>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    city: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      weatherData: null,
      loading: true,
    };
  },
   watch: {
    city: {
      immediate: true,
      handler(newCity) {
        this.fetchWeatherData(newCity);
      },
    },
  },
  created() {
    this.fetchWeatherData();
  },
  methods: {
    async fetchWeatherData (city) {
    this.loading = true;
      try {
        const apiKey = '482944e26d320a80bd5e4f23b3de7d1f'; 
        const url = `https://api.openweathermap.org/data/2.5/onecall?lat=${this.city.lat}&lon=${this.city.lon}&exclude=minutely&units=metric&appid=${apiKey}`;

        const response = await axios.get(url);
        console.log('response', response.data);
        //return response;
        this.weatherData = response.data;
        this.weatherDataCurrent = response.data.current.weather[0];
        console.log('response-weather-current', response.data.current.weather[0]);
      } catch (error) {
        console.error('Error fetching weather data:', error);
        this.weatherData = null;
      } finally {
        this.loading = false;
      }
    },
    refreshData() {
      this.fetchWeatherData(this.city);
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
.weather-container {
    background-image: linear-gradient(#01699c, #0690b9, #bdb0a6);
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
.hours-details:nth-child(4) {
    border-right: none;
}
.days-details:nth-child(5) {
    border-bottom: none;
}
.refreshbtn {
    background: #1365c0;
    color: white;
    padding: 5px;
    margin: auto;
    text-align: right;
    width: 100%;
}
</style>
