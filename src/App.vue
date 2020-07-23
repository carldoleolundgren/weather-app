<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-toolbar-title> Weather App </v-toolbar-title>
      <v-spacer>
        <v-text-field 
          label="Search by location" 
          class="mt-7 ml-16 mr-11" 
          solo 
          light 
          dense 
          v-model="cityInput"
          @keydown.enter="getWeather(cityInput)"
        ></v-text-field>
      </v-spacer>
      <v-btn
        href="https://github.com/carldoleolundgren/weather-app"
        target="_blank"
        text
      >
        <span>Link to Github</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-card class="mx-auto mt-16" max-width="600">
        
        <v-list-item> <!-- City, date, description -->
          <v-list-item-content class="mt-2 ml-16">
            <v-list-item-title class="headline text-no-wrap">
              {{cityName}}, {{cityState ? cityState + ', ' : ''}} {{countryCode}}
            </v-list-item-title>

            <v-list-item-subtitle>
              {{localTime}}
            </v-list-item-subtitle>
            <v-list-item-subtitle>
              {{description}}
            </v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
        
        <v-card-text> <!-- center: degrees, feels like, min/max -->
          
          <v-row>
            <v-col cols="6" class="ml-16">
              <v-list-item>
                <v-list-item-content >
                  <v-list-item-title class="display-4">
                    {{usingMetricUnits ? currentTempCelsius : currentTemp}}&deg;
                    </v-list-item-title>
                  <v-list-item-subtitle class="mt-3 subtitle-1">
                    Feels like {{usingMetricUnits ? feelTempCelsius : feelTemp}}&deg;
                  </v-list-item-subtitle>
                  <v-list-item-subtitle class="mt-3 subtitle-1">
                    Min: {{usingMetricUnits ? minTempCelsius : minTemp}}&deg;
                    / Max: {{usingMetricUnits ? maxTempCelsius : maxTemp}}&deg;
                  </v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>

            </v-col>

            <v-col cols="3">
              <v-img :src=iconSRC class="filter"> </v-img>
            </v-col>
          </v-row>
        </v-card-text>

        <v-list-item class="ml-16"> <!-- wind -->
          <v-list-item-icon>
            <v-icon>
              mdi-weather-windy
            </v-icon>
          </v-list-item-icon>
          <v-list-item-subtitle class="subtitle-1"> 
            {{usingMetricUnits ? windSpeedMetric : windSpeed}} 
            {{usingMetricUnits ? 'm/s' : 'mph'}}
          </v-list-item-subtitle>
        </v-list-item>
        
        <v-list-item class="ml-16"> <!-- humidity -->
          <v-list-item-icon>
            <v-icon>
              mdi-water-percent
            </v-icon>
          </v-list-item-icon>
          <v-list-item-subtitle class="subtitle-1">
            {{humidity}}%
          </v-list-item-subtitle>
        </v-list-item>
        
        <v-divider></v-divider>
        
        <v-card-actions>
          <v-btn text class="mx-auto" @click="usingMetricUnits = true; storeUnits();">&deg;C</v-btn>
          <v-btn text class="mx-auto" @click="usingMetricUnits = false; storeUnits();">&deg;F</v-btn>
        </v-card-actions>
        
      </v-card>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      apiKey: 'ad5c13b60ff4ac2c13b2879d0cbd2c1e',
      cityInput: null,
      usingMetricUnits: false,
      weatherData: null,
      cityName: null,
      cityState: null,
      countryCode: null,
      description: null,
      currentTemp: null,
      feelTemp: null,
      minTemp: null,
      maxTemp: null,
      windSpeed: null,
      humidity: null,
      iconSRC: 'https://openweathermap.org/img/wn/01d@2x.png',
      localTime: null,
    }
  },
  computed: {
    currentTempCelsius() {
      return Math.round((this.currentTemp - 32) * (5/9));
    },
    feelTempCelsius() {
      return Math.round((this.feelTemp - 32) * (5/9));      
    },
    minTempCelsius() {
      return Math.round((this.minTemp - 32) * (5/9));      
    },
    maxTempCelsius() {
      return Math.round((this.maxTemp - 32) * (5/9));      
    },
    windSpeedMetric() {
      return Math.round(this.windSpeed / 2.237);
    }
  },
  methods: {
    async getWeather(input) {
      this.cityInput = null;
      this.cityState = null;

      try {
        let name = input.split(',')[0].toLowerCase();
        let stateOrCountry = null;
        if (input.indexOf(',') !== -1) {
          stateOrCountry = input.split(',')[1].slice(1).toLowerCase();
        }

        let cityList = await fetch(`./city.list.json`, {mode: 'cors'});
        let cityListJSON = await cityList.json();
        let cityIndex;
        
        let USLocation = (cityListJSON.findIndex(city => city.state.toLowerCase() === stateOrCountry) !== -1) 
          ? true : false;

        let InternationalLocation = (cityListJSON.findIndex(city => city.country.toLowerCase() === stateOrCountry) !== -1)
          ? true : false;

        if (stateOrCountry === null) {
          cityIndex = cityListJSON.findIndex(city => 
              city.name.toLowerCase() === name 
          )
        } else if (USLocation) {
          cityIndex = cityListJSON.findIndex(city => 
              city.name.toLowerCase() === name 
              && city.state.toLowerCase() === stateOrCountry
          )
          this.cityState = cityListJSON[cityIndex].state;
        } else if (InternationalLocation) {
          cityIndex = cityListJSON.findIndex(city => 
              city.name.toLowerCase() === name 
              && city.country.toLowerCase() === stateOrCountry
          )
        }

        let weatherResponse = await fetch(
          'http://api.openweathermap.org/data/2.5/weather?id=' 
          + cityListJSON[cityIndex].id
          + '&APPID=ad5c13b60ff4ac2c13b2879d0cbd2c1e&units=imperial',
          {mode: 'cors'}
        )
        this.weatherData = await weatherResponse.json(); 
        this.countryCode = cityListJSON[cityIndex].country;        
        this.cityName = cityListJSON[cityIndex].name;
        this.description = this.weatherData.weather[0].description[0].toUpperCase()
                            + this.weatherData.weather[0].description.slice(1);
        this.currentTemp = Math.round(this.weatherData.main.temp);
        this.feelTemp = Math.round(this.weatherData.main.feels_like)
        this.minTemp = Math.round(this.weatherData.main.temp_min);
        this.maxTemp = Math.round(this.weatherData.main.temp_max);
        this.windSpeed = Math.round(this.weatherData.wind.speed);
        this.humidity = Math.round(this.weatherData.main.humidity);
        this.iconSRC = 'https://openweathermap.org/img/wn/'
          + this.weatherData.weather[0].icon
          + '@2x.png';

        let timezoneResponse = await fetch(
          'https://api.timezonedb.com/v2.1/get-time-zone?key=0U0Q18WRJST6&format=json&by=position&lng='
          + this.weatherData.coord.lon
          + '&lat='
          + this.weatherData.coord.lat, 
          {mode: 'cors'}
        )
        let timezoneData = await timezoneResponse.json()

        this.localTime = new Date().toLocaleString('en-US', 
          { timeZone: timezoneData.zoneName, weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' }
        );
      } catch (err) {
        //alert('Cannot find the city you searched for. Please try another location.')
        this.getWeather("Cambridge, MA")
      } 
    },
    storeUnits() {
      localStorage.setItem('storedUnits', JSON.stringify(this.usingMetricUnits))
    }
  },
  created() {
    if (localStorage.getItem('storedUnits')) {
      this.usingMetricUnits = JSON.parse(localStorage.getItem('storedUnits'))
    }
    this.getWeather("Cambridge, MA");
  }
};
</script>

<style scoped>
.filter {
  filter: drop-shadow(0px 0px 5px rgb(134, 134, 134));
}
</style>