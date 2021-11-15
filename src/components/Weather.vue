<template>
    <div class="container">
        <h2>Weather in {{chooseCityName}}</h2>

        <div class="row content">
                <div class="col-md-5">
                <form @submit="addCity" class="choose-city">
                    <h3>Add city</h3>
                    <input class="select" type="text" v-model="addCityName">
                    <input class="button city-button" type="submit" value="Add">
                </form>
            </div>
            <div class="col-md-5">
                <div class="choose-city">
                    <h3>Choose city</h3>
                    <select class="select" v-model="chooseCityName" @change="getWeatherByCity">
                        <option v-for="(city, index) in cityList" :key="index">{{ city }}</option>
                    </select>
                </div>
            </div>
        </div>

        <div class="row content2">
            <div class="col-md-5">
                <div class="block">
                    <div v-if="!errorMsg">
                        <div class="row">
                            <div class="col-md-4">
                                <img :src="require('../assets/img/icons/' + currentWeather.weather[0].main + '.svg')" class="weather-icon" alt="">
                            </div>
                            <div class="col-md-8">
                                <h3 class="temp">{{ round(currentWeather.main.temp) }} &#8451;</h3>
                                <h3>{{ currentWeather.weather[0].description }} </h3>
                            </div>     
                        </div>
                        <div class="row">
                            <div class="col-md-6">
                                <h3>Main:</h3>
                                <p>Feels like: {{ round(currentWeather.main.feels_like) }} &#8451;</p>
                                <p>Min temp: {{ round(currentWeather.main.temp_min) }} &#8451;</p>
                                <p>Max temp: {{ round(currentWeather.main.temp_max) }} &#8451;</p>
                                <p>Pressure: {{ round(currentWeather.main.pressure) }}</p>
                                <p>Humidity: {{ round(currentWeather.main.humidity) }}</p>
                                <p>Visibility: {{ round(currentWeather.visibility) }}</p>
                            </div>
                            <div class="col-md-6">
                                <h3>Wind:</h3>
                                <p>Speed: {{ round(currentWeather.wind.speed) }}</p>
                                <p>Deg: {{ round(currentWeather.wind.deg) }}</p>
                            </div>
                        </div>
                    </div>
                    <div v-if="errorMsg">
                        {{ errorMsg }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Weather',
  data: function () {
    return {
        cityList: ['Zaporizhzhia', 'Kyiv'],
        addCityName: '',
        chooseCityName: 'Zaporizhzhia',
        currentWeather: {},
        userLocation: {},
        errorMsg: ''
    }
  },
  beforeMount: function () {
      this.getWeatherByCity();
  },
  mounted: function () {
        const callbackUserLocation = (position) => {
            this.getWeatherByCoords(position);
        }

        window.navigator.geolocation.getCurrentPosition(callbackUserLocation);

      if (localStorage.getItem('cityList')) this.cityList = JSON.parse(localStorage.getItem('cityList'));
  },
  methods: {
      addCity: function(e) {
          this.cityList.push(this.addCityName);
          this.addCityName = '';
          localStorage.setItem('cityList', JSON.stringify(this.cityList));
          e.preventDefault();
      },
      getWeatherByCoords: function(coords) {
            axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${coords.coords.latitude}&lon=${coords.coords.longitude}&appid=afc5bcfd369def6a2e2be41d49362ca6&units=metric`).then((response) => {
                this.chooseCityName = response.data.name;
                this.currentWeather = response.data;
                this.cityList.push(response.data.name);
            });               
      },
      getWeatherByCity: function(e) {
        axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.chooseCityName}&appid=afc5bcfd369def6a2e2be41d49362ca6&units=metric`).then((response) => {
            this.errorMsg = '';
            console.log(response.data);
               this.currentWeather = response.data;
          }).catch(() => {
            this.errorMsg = 'Такого міста не існує';
        });

          if(e) e.preventDefault();
  },
  round: function(value) {
      return Math.round(value);
  }
}
}
</script>

<style>
    .content {
        display: flex;
        justify-content: center;
        margin-bottom: 50px;
    }

    .content2 {
        padding: 20px;
        display: flex;
        justify-content: center;
    }

    .block {
        border-radius: 50px;
        background: rgb(231, 231, 231);
        padding: 20px;
        font-size: 18px;
    }

    .city-button {
        margin-left: 10px;
    }
    .weather-icon {
        width: 100%;
    }
</style>
