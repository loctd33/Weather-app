<template>
  <div class="container-fluid">
    <div class="weather-app row">
      <header class="header col-12 d-flex flex-row justify-content-between align-items-center">
        <div class="header-logo">
          <h1>
            <svg xmlns="http://www.w3.org/2000/svg" class="logo-icon" viewBox="2.136000156402588 1.9980000257492065 17.86400032043457 17.00200080871582">
              <g class="icon">
                <svg xmlns="http://www.w3.org/2000/svg" class="icon" role="button">
                  <path d="M16.752 13.63l1.06 3.183-3.181-1.06zM11.5 15a4.5 4.5 0 0 0 4.5-4.5 4.375 4.375 0 0 0-.29-1.502l-7.44 4.613A4.46 4.46 0 0 0 11.5 15zM17 9v3l3-1.5zm-5.5 10l1.5-3h-3zm-5.77-3.814l-.543 1.627 3.182-1.06-1.36-1.36zM16.71 6.85l-.686-1.186-1.787 1.033V4.404H13l.001 2.983-.02.036L10.2 9.145V5.371l2.306-1.331-.618-1.071-1.688.974V1.998H8.8v1.945l-1.688-.974-.618 1.07L8.8 5.372v3.766L5.999 7.403 6 4.404H4.763v2.285l-1.784-1.03-.69 1.193 1.835 1.06-1.988 1.146.618 1.071L5.36 8.626l3.07 1.889-3.04 1.872-2.636-1.52-.618 1.07 2.017 1.164-1.849 1.068.686 1.186z" vector-effect="non-scaling-stroke">
                  </path>
                </svg>
              </g>
            </svg>
            <span class="d-none d-sm-block">Weather</span>
          </h1>
          
        </div>
        <div class="header-search search-box col-6 col-sm-4">
          <input 
            type="text" 
            class="search-bar" 
            placeholder="Search..."
            v-model="query"
            @keyup.enter="fetchWeather()"
          />
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-search" viewBox="0 0 16 16">
            <path d="M11.742 10.344a6.5 6.5 0 1 0-1.397 1.398h-.001c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1zM12 6.5a5.5 5.5 0 1 1-11 0 5.5 5.5 0 0 1 11 0z"/>
          </svg>
        </div>
        <div class="header-btn">
          <input type="checkbox" class="checkbox" id="checkbox" @click="temp_c = !temp_c;  fetchWeather()">
          <label for="checkbox" class="label">
            <div class="fas fa-moon">°C</div>
            <div class="fas fa-sun">°F</div>
            <div class='ball'></div>
          </label>
        </div>
      </header>

      <main class="d-flex flex-column justify-content-between mt-4 mt-lg-0">
        <div class="weather d-flex flex-column flex-lg-row justify-content-between" v-if="typeof weather.main != 'undefined'">
          <div class="weather-location location-box text-center">
            <div class="location">{{ weather.name == 'Turan' ? 'Da Nang' : weather.name }}, {{ weather.sys.country }}</div>
            <div class="date">{{ dateBuilder() }}</div>
            <div class="temp">
              <span v-if="temp_c">{{ Math.round(weather.main.temp) }}°c</span>
              <span v-else>{{ Math.round(weather.main.temp) }}°f</span>
            </div>
          </div>
  
          <div class="weather-main">
            <img :src="getUrlImages" alt="">
            <div class="weather-main__description">{{ weather.weather[0].main }}</div>
          </div>
        </div>
  
        <div class="d-flex flex-column flex-lg-row justify-content-between align-items-center align-items-lg-end">
          <div class="weather-wind mb-4 mt-4">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-wind" viewBox="0 0 16 16"> <path d="M12.5 2A2.5 2.5 0 0 0 10 4.5a.5.5 0 0 1-1 0A3.5 3.5 0 1 1 12.5 8H.5a.5.5 0 0 1 0-1h12a2.5 2.5 0 0 0 0-5zm-7 1a1 1 0 0 0-1 1 .5.5 0 0 1-1 0 2 2 0 1 1 2 2h-5a.5.5 0 0 1 0-1h5a1 1 0 0 0 0-2zM0 9.5A.5.5 0 0 1 .5 9h10.042a3 3 0 1 1-3 3 .5.5 0 0 1 1 0 2 2 0 1 0 2-2H.5a.5.5 0 0 1-.5-.5z"/> </svg>
            Wind: 
            Deg {{ weather.wind.deg }},
            Speed {{ weather.wind.speed }}
          </div>
          <!-- Component Map -->
          <MapBox :query="query"></MapBox>
        </div>
      </main>

    </div>
  </div>
</template>

<script>

import MapBox from './components/MapBox.vue'

export default {
  name: 'app',
  components: {
    MapBox
  },
  data () {
    return {
      api_key: 'e5505f582d751cc696fed1b037972cfe',
      url_base: 'https://api.openweathermap.org/data/2.5/weather',
      // url_forecast: 'https://api.openweathermap.org/data/2.5/forecast',
      query: 'Da Nang',
      old_query: '',
      weather: {},
      temp_c: true,
    }
  },
  created() {
    this.fetchWeather();
    this.old_query = this.query;
    this.query = '';
  },
  computed: {
    getUrlImages() {
      return `https://openweathermap.org/img/wn/${this.weather.weather[0].icon}@4x.png`
    }
  },
  methods: {
    async fetchWeather () {
        let response = await fetch(`${this.url_base}?q=${this.query === "" ? this.old_query : this.query}&units=${this.temp_c ? "metric": "imperial"}&appid=${this.api_key}`)
        
        if(response.ok) {
          let json = await response.json();
          this.weather = await json;
        }
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/bg-weather.png');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

.weather-app {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

header {
  height: fit-content;
}

.header-logo svg {
  width: 48px;
}

.header-logo span {
  font-size: 12px;
}

.header-search.search-box {
  position: relative;
}

.header-search svg {
  position: absolute;
  right: 20px;
  top: 20px;
  cursor: pointer;
} 

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  
  color: #313131;
  font-size: 16px;

  appearance: none;
  border:none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.checkbox {
  opacity: 0;
  position: absolute;
}

.label {
  position: relative;
  width: 48px;
  height: 26px;

  display: flex;
  align-items: center;
  justify-content: space-between;

  border-radius: 50px;
  padding: 5px 8px;
  cursor: pointer;

  background-color: #111;
  transform: scale(1.5);
}

.ball {
  position: absolute;
  width: 22px;
  height: 22px;

  top: 2px;
  right: 2px;
  
  border-radius: 50%;
  background-color: #ccc;
  transition: transform 0.2s linear;
}

/*  target the elemenent after the label*/
.checkbox:checked + .label .ball{
  transform: translateX(-22px);
}

.fas {
  font-size: 12px;
}

.fa-moon {
  color: pink;
}

.fa-sun {
  color: yellow;
}

.weather-location .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.weather-location .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
}

.weather-location .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color:rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-main {
  text-align: center;
}

.weather-main .weather-main__description {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-wind {
  color: #FFF;
}

@media only screen and (max-width: 576px) {
  .weather-app {
    padding: 12px;
    align-items: flex-start;
  }

  .header-logo svg {
    width: 32px;
  }

  .search-box .search-bar {
    padding: 6px 12px;
    font-size: 13px;
  }

  .header-btn .label {
    width: 36px;
    height: 18px;
    padding: 5px 6px;
  }

  .header-btn .fas {
    font-size: 9px;
  }

  .header-btn .ball {
    width: 14px;
    height: 14px;
  }

  .checkbox:checked + .label .ball{
    transform: translateX(-18px);
  }

  .weather-location .temp {
    font-size: 64px;
    padding: 4px 16px;
  }

  .weather-location .location {
    font-size: 26px;
    font-weight: 500;
  }

  .weather-location .date {
    font-size: 13px;
  }

  .weather-main img {
    width: 110px;
  }

  .weather-main .weather-main__description {
    font-size: 26px;
  }

  .weather-wind {
    font-size: 13px;
  }
}
</style>