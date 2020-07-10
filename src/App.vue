<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name}}, {{weather.sys.country}}</div>
          <div class="date">{{dateBuilder()}}</div>
        </div>
        <div class="weather-box" @mouseover="mouseEnter" @mouseleave="mouseLeave">
          <div v-show="toShowOffHover" class="offHover">
            <span class="temp">{{Math.round(weather.main.temp)}}°C</span>
            <div class="weather">{{weather.weather[0].description}}</div>
            <div class="weather-range">
              <p>{{Math.round(weather.main.temp_min)}}°C</p>
              <p>{{Math.round(weather.main.temp_max)}}°C</p>
            </div>
          </div>

          <div class="onHover" v-show="toShowOnHover">
            <p>FEELS LIKE: {{Math.round(weather.main.feels_like)}}°C</p>
            <p>HUMIDITY: {{Math.round(weather.main.humidity)}}%</p>
            <p>PRESSURE: {{Math.round(weather.main.pressure)}} hPA</p>
            <p>WIND SPEED: {{Math.round(weather.wind.speed)}} m/s</p>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: "0272b8fd47471b0aa4c64091d864e8bd",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      toShowOnHover: false,
      toShowOffHover: true
    };
  },
  methods: {
    mouseEnter: function() {
      this.toShowOnHover = true;
      this.toShowOffHover = false;
    },
    mouseLeave: function() {
      this.toShowOnHover = false;
      this.toShowOffHover = true;
    },
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then(res => {
            return res.json();
          })
          .then(this.setResults)
          .then(this.setIcon);
        this.query = "";
      }
    },

    setResults(results) {
      this.weather = results;
    },
    setIcon() {
      if (document.querySelector(".temp").nextSibling.alt === "weather-icon") {
        document
          .querySelector(".weather-box")
          .removeChild(document.querySelector(".temp").nextSibling);
      }
      document.querySelector(".weather-box");
      let icon = document.createElement("img");
      icon.src =
        "http://openweathermap.org/img/wn/" +
        this.weather.weather[0].icon +
        "@4x.png";
      icon.alt = "weather-icon";
      document
        .querySelector(".offHover")
        .insertBefore(icon, document.querySelector(".weather"));
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return ` ${month} ${date}, ${year}`;
    }
  }
};
</script>

<style>
* {
  font-family: "Helvetica Neue", Helvetica, sans-serif;
}

#app {
  background: linear-gradient(#0abee6, #48484a, #0abee6);
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}

#app.warm {
  background: linear-gradient(#e66465, white, #e66465);
}

.hidden {
  display: none;
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.weather-range {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-content: flex-end;
  width: 100%;
  font-size: 3vw;
  color: white;
  height: inherit;
}

.onHover {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-content: space-evenly;
  height: inherit;
  width: 100%;
}

.onHover > p {
  color: white;
  font-size: 2vw;
  align-self: center;
  margin: 5%;
}

.search-box {
  width: 100%;
  margin-bottom: 20%;
}

.search-box .search-bar {
  display: block;
  width: 50%;
  padding: 15px;
  margin-left: auto;
  margin-right: auto;

  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0 0 0 0;
  transition: 0.2s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 255);
  border-radius: 25px 25px 25px 25px;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  height: 50vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  width: 60%;
  margin-left: auto;
  margin-right: auto;
  margin-top: 10%;
}

.offHover {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
  margin-left: auto;
  margin-right: auto;
  height: inherit;
  width: inherit;
}

.temp {
  color: white;
  font-size: 20vw;
  margin: 2%;
  text-align: center;
}

.weather-box .weather {
  color: #fff;
  width: 100%;
  height: 30%;
  font-size: 5vw;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  text-align: center;
}
</style>
