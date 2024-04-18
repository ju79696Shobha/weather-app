<template>
  <div id="app">
    <div>
      <SearchbarVue  @citySelected="handleCitySearch" />
      <div class="city-container">
        <button
          v-for="(city, index) in cities"
          :key="index"
          :class="{ active: index === selectedCityIndex }"
          @click="selectCity(index)"
        >
          {{ city.name }}
        </button>
      </div>
      
      <Weather
        :city="cities[selectedCityIndex]"
      />
    </div>
  </div>
</template>

<script>
import SearchbarVue from './views/Searchbar.vue';
import Weather from './views/Weather.vue';

export default {
  components: {
    SearchbarVue,
    Weather,
  },
  data() {
    return {
      cities: [
        { name: 'Rio de Janeiro', lat: -22.9068, lon: -43.1729 },
        { name: 'Beijing', lat: 39.9042, lon: 116.4074 },
        { name: 'Los Angeles', lat: 34.0522, lon: -118.2437 },
      ],
      selectedCityIndex: 0,
    };
  },
  methods: {
    selectCity(index) {
      this.selectedCityIndex = index;
    },
    handleCitySearch(city) {
      this.selectedCity = city;
      this.fetchWeatherData(city);
    },
  },
};
</script>

<style>
h1 {
    color: white;
    background: #0690b9;
    text-align: left;
    padding: 10px;
}
h2 {
  margin-top: 0;
}
#app {
  padding: 0;
  font-family: sans-serif;
}
.tabs {
  display: flex;
  list-style: none;
  padding: 0;
}
.tabs li {
  padding: 10px;
  cursor: pointer;
}
.tabs li.active {
  font-weight: bold;
}
.city-container{
  display: flex;
  justify-content: space-around;
  background-color: #f4f4f5;
  padding-top: 10px;
}
.city-container button {
  margin-right: 8px;
  background: none;
    color: #737373;
    font-size: 16px;
    padding: 10px;
    border: none;
    font-weight: 600;
  text-transform: uppercase;
}
.city-container button.active {
  color: black;
  font-weight: bold;
  border-bottom: 3px solid #fa4a22;
}
</style>

