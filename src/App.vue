<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-toolbar-title> Weather App </v-toolbar-title>
      <v-spacer>
        <v-text-field label="Search by location" class="mt-7 ml-16 mr-11" solo light dense 
          @keydown.enter="getWeather"
        ></v-text-field>
      </v-spacer>
      <v-btn
        href=""
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
              {{cityName}}, {{countryCode}}
            </v-list-item-title>

            <v-list-item-subtitle>
              {{new Date().toLocaleString()}}, {{description}}
            </v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
        
        <v-card-text> <!-- center: degrees, feels like, min/max -->
          
          <v-row>
            <v-col cols="6" class="ml-16">
              <v-list-item>
                <v-list-item-content >
                  <v-list-item-title class="display-4">
                    {{currentTemp}}&deg;
                    </v-list-item-title>
                  <v-list-item-subtitle class="mt-3 subtitle-1">
                    Feels like {{feelTemp}}&deg;
                  </v-list-item-subtitle>
                  <v-list-item-subtitle class="mt-3 subtitle-1">
                    Min: {{minTemp}}&deg;
                    / Max: {{maxTemp}}&deg;
                  </v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>

            </v-col>

            <v-col cols="3">
              <v-img :src=iconSRC>{{weatherData.weather[0].icon}}</v-img>
            </v-col>
          </v-row>
        </v-card-text>

        <v-list-item class="ml-16">
          <v-list-item-icon>
            <v-icon>
              mdi-weather-windy
            </v-icon>
          </v-list-item-icon>
          <v-list-item-subtitle class="subtitle-1"> 
            {{windSpeed}} 
            {{usingMetricUnits ? 'km/h' : 'mph'}}
          </v-list-item-subtitle>
        </v-list-item>
        
        <v-list-item class="ml-16">
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
          <v-btn text class="mx-auto" @click="isCelsius = true">&deg;C</v-btn>
          <v-btn text class="mx-auto" @click="isCelsius = false">&deg;F</v-btn>
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
      usingMetricUnits: false,
      weatherData: null,
      cityName: null,
      countryCode: null,
      description: null,
      currentTemp: null,
      feelTemp: null,
      minTemp: null,
      maxTemp: null,
      windSpeed: null,
      humidity: null,
      iconSRC: null
    }
  },
  methods: {
    async getWeather(cityInput) {
      let cityName = cityInput.split(',')[0].toLowerCase();
      let cityState = cityInput.split(',')[1].slice(1).toLowerCase();

      let cityList = await fetch(`./city.list.copy.json`, {mode: 'cors'});
      let cityListJSON = await cityList.json();
      let cityID = cityListJSON.findIndex(
        city => 
          city.name.toLowerCase() === cityName 
          && city.state.toLowerCase() === cityState
        )
      
      let response = await fetch(
        'http://api.openweathermap.org/data/2.5/weather?id=' 
        + cityListJSON[cityID].id
        + '&APPID=ad5c13b60ff4ac2c13b2879d0cbd2c1e&units=imperial',
        {mode: 'cors'}
      )
      this.weatherData = await response.json(); 
      
      this.cityName = this.weatherData.name;
      this.countryCode = this.weatherData.sys.country
      this.description = this.weatherData.weather[0].description;
      this.currentTemp = Math.round(this.weatherData.main.temp);
      this.feelTemp = Math.round(this.weatherData.main.feels_like)
      this.minTemp = Math.round(this.weatherData.main.temp_min);
      this.maxTemp = Math.round(this.weatherData.main.temp_max);
      this.windSpeed = Math.round(this.weatherData.wind.speed);
      this.humidity = Math.round(this.weatherData.main.humidity);
      this.iconSRC = 'https://openweathermap.org/img/wn/'
        + this.weatherData.weather[0].icon
        + '@2x.png';

      console.log(this.weatherData)
      
    }
  },
  components: {

  },
  created() {
    this.getWeather("East Hartford, CT");
  }
};
</script>

<style scoped>

</style>