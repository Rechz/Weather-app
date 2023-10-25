<template>
  <div id="app" class="app" :class="bgImage">
    <main>

      <!---------- the search bar display----------->
    <header class="head">
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Select a place" 
          v-model="query"
          @keypress.enter="fetchWeather"
        />
      </div>
    </header>
   
    <!-- displays weather details -->
    <div class="box-width" v-if="isInputvalid && (typeof weather.main != 'undefined')">
            <!-- display specific location and date -->
      <div class="weather-wrap">
          <div class="location-box">
            <div class="location">{{weather.name}}, {{ weather.sys.country}}</div>
            <div class="date">{{setDate()}}</div>
          </div>
      <!-- display temperature and weather status -->
          <div class="weather-box">
            <div class="temp">{{ Math.round(weather.main.temp)}}°C</div>
            <div class="weather">{{weather.weather[0].description}}</div> 
            <img class="weather-desc" :src="'https://openweathermap.org/img/wn/'+weather.weather[0].icon+'@2x.png'">      
          </div>
          <!-- display min & max temperatures -->
        <div class="grid-box">
            <div class="box-temp">
              <p>Feels like: <br>{{ Math.round(weather.main.feels_like)}}°C</p>
            </div>
            <div class="box-temp">
              <p>Humidity: <br>{{ Math.round(weather.main.humidity)}}%</p>
            </div>
            <div class="box-temp">
              <p>Minimum: <br>{{ Math.round(weather.main.temp_min)}}°C </p>
            </div>
            <div class="box-temp">
              <p>Maximum: <br>{{ Math.round(weather.main.temp_max)}}°C</p>
            </div>
        </div>
      </div>
    </div>
    <!-- Error-message -->
    <div class="error-box" v-else-if="!isInputvalid">
      <div class="error">Error {{ errorCode }}: {{ errorMessage }}</div>
      <h2>Please enter a valid place.</h2>
    </div>
    </main>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',
  data() {
    return {
      api_key : 'b5c861f0d7af972d4c0ac0242df0e265', //This is a variable storing an API key.
      url_base : 'https://api.openweathermap.org/data/2.5/', //It's a variable storing the base URL for the OpenWeatherMap API.
      query : '',  //This is a variable that will store the user's input query for weather data.
      weather : {}, //This variable will be used to store the retrieved weather data.
      clearCond : false, 
      cloudCond : false, 
      rainCond : false, 
      thunderCond : false, 
      mistCond : false, 
      drizzleCond : false, 
      snowCond : false,
      errorMessage: '', 
      errorCode: '', 
      isInputvalid: true
    };
  },

  methods: {
    async fetchWeather() {
      this.resetAll();
      try{
        const response = await axios.get(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`); // fetches weather data
        if (response.data)
          {
          this.weather = response.data;
          this.checkWeatherDescription(this.weather.weather[0].id) // function to set necessary conditions to change background image using weather description
          }
      }
      catch(error) { // to know errors 
        this.isInputvalid = false;
        this.errorCode = error.response.data.cod;
        this.errorMessage = error.response.data.message;
        console.log(error)
      }
    },

    checkWeatherDescription(weather_id) {
      if (weather_id === 800)
            { this.clearCond = true; }
          else if (weather_id <= 804 && weather_id >= 801)
            { this.cloudCond = true; }   
          else if (weather_id <= 781 && weather_id >= 701)
            { this.mistCond = true; } 
          else if (weather_id <= 622 && weather_id >= 600)
            { this.snowCond = true; } 
          else if (weather_id <= 531 && weather_id >= 500)
            { this.rainCond = true; } 
          else if (weather_id <= 321 && weather_id >= 300)
            { this.drizzleCond = true; }
          else if (weather_id <= 232 && weather_id >= 200)
            { this.thunderCond = true; }
    },

    resetAll() {
      this.clearCond = false; this.cloudCond = false; this.mistCond = false; this.rainCond = false; this.thunderCond = false;
      this.drizzleCond = false; this.snowCond = false; this.isInputvalid = true;  this.errorMessage = '', this.errorCode = '';
    },

    setDate() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let day = days[d.getDay()];
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      let date = d.getDate();
      if (date === 1 || date === 21 || date === 31)
      { date = date + 'st';  }
      else if (date === 2 || date === 22)
      { date = date + 'nd';  }
      else if (date === 3 || date === 23)
      {date = date + 'rd'; }
      else 
      { date = date + 'th'; }
      return `${day}, ${date} ${month}, ${year}`;
    }
  },

    computed : {
    bgImage() { // setting background image
      return {
        'clear' : this.clearCond,
        'mist'  : this.mistCond,
        'rain'  : this.rainCond,
        'thunderstorm' : this.thunderCond,
        'drizzle' : this.drizzleCond,
        'snow' : this.snowCond,
        'cloud' : this.cloudCond };
      }}
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.head {
  height: 20vh;
  width: 80%;
  margin: 0 auto;
  background-color:rgba(255, 255, 255, 0.25);
  border-radius: 16px; 
}
body {
  font-family: 'montserrat', sans-serif;}

.app {
  background-color: rgb(15, 28, 93);
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
.clear{
  background-image: url('./assets/clear.jpeg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
.cloud{
  background-image: url('./assets/cloudy.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
.mist{
  background-image: url('./assets/mist.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
.rain{
  background-image: url('./assets/rain.jpeg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
.thunderstorm{
  background-image: url('./assets/thuderstorm.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
.drizzle{
  background-image: url('./assets/drizzle.jpeg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
.snow{
  background-image: url('./assets/snow.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
}
.search-box {
  width: 100%;
  padding-top: 20px;
}
.search-box .search-bar {
  display: block;
  width: 75%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border:none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
  margin: 20px auto;
}
.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}
.location-box .location {
  color: #FFF;
  font-size: 50px;
  font-weight: 600;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  margin-top: 10px;
}
.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 90px;
  font-weight: 600;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color:rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #FFF;
  font-size: 30px;
  font-weight: 150;
  font-style: italic;
  text-shadow: 3px 3px rgba(0, 0, 0, 0.10);
}
.grid-box {
  display: grid;
  grid-template-columns: 200px 200px;
  justify-content: center;
  gap: 5px;
}
.box-temp{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  font-weight: 80;
  padding: 0 10px;
  background-color:rgba(255, 255, 255, 0.099);
  border-radius: 10px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  text-align: center;
  color:white;
}
.box-width {
  width: 50%;
  margin: 0 auto;
}
.weather-desc {
  height:70px;
  text-align: center;
  margin: 0;
}
.error{
  color: white;
  font-size: 30px;
  font-weight: 100;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  margin-top: 10px;
  padding: 20px;
}
.error-box h2{ 
  color: white;
  font-weight: 50;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
</style>