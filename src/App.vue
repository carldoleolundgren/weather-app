<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-toolbar-title> Weather App </v-toolbar-title>
      <v-spacer>
        <v-text-field label="Search by location" class="mt-7 ml-16 mr-11" solo light dense 
          v-model="cityInput"
          @keydown.enter="getWeather(cityInput)"
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
              {{cityName}}, {{cityState ? cityState + ', ' : ''}} {{countryCode}}
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
              <v-img :src=iconSRC> </v-img>
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
      iconSRC: 'https://openweathermap.org/img/wn/01d@2x.png'
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

        let response = await fetch(
          'http://api.openweathermap.org/data/2.5/weather?id=' 
          + cityListJSON[cityIndex].id
          + '&APPID=ad5c13b60ff4ac2c13b2879d0cbd2c1e&units=imperial',
          {mode: 'cors'}
        )
        this.weatherData = await response.json(); 
        this.countryCode = cityListJSON[cityIndex].country;        
        this.cityName = cityListJSON[cityIndex].name;
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
      } catch (err) {
        alert('Cannot find the city you searched for. Please try another location.')
        this.getWeather("Hartford, CT")
      } 
    }
  },
  created() {
    this.getWeather("Manchester");
  }
};
</script>

<style scoped>

</style>