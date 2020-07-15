<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-toolbar-title> Weather App </v-toolbar-title>
      <v-spacer>
        <v-text-field label="Search by location" class="mt-7 ml-16 mr-11" solo light></v-text-field>
      </v-spacer>
      <v-btn
        href="#"
        target="_blank"
        text
      >
        <span class="mr-2">Link to Github</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-card class="mx-auto mt-16" max-width="600">
        
        <v-list-item> <!-- City, date, description -->
          <v-list-item-content class="mt-2 ml-16">
            <v-list-item-title class="headline text-no-wrap">
              Bogota
            </v-list-item-title>

            <v-list-item-subtitle>
              Date, Description
            </v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
        
        <v-card-text> <!-- center: degrees, feels like, min/max -->
          
          <v-row>
            <v-col cols="6" class="ml-16">
              <v-list-item>
                <v-list-item-content >
                  <v-list-item-title class="display-4">
                    56&deg;
                    </v-list-item-title>
                  <v-list-item-subtitle class="mt-3 subtitle-1">
                    Feels like 
                  </v-list-item-subtitle>
                  <v-list-item-subtitle class="mt-3 subtitle-1">
                    Min: 50&deg;
                    / Max: 60&deg;
                  </v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>

            </v-col>

            <v-col cols="3">
              <v-img src="https://openweathermap.org/img/wn/10n@2x.png"></v-img>
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
            12 km/h
          </v-list-item-subtitle>
        </v-list-item>
        
        <v-list-item class="ml-16">
          <v-list-item-icon>
            <v-icon>
              mdi-water-percent
            </v-icon>
          </v-list-item-icon>
          <v-list-item-subtitle class="subtitle-1">
            65%
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

  components: {

},

  data: () => ({
    //
  }),
};
</script>

<style scoped>
.test {
  text-align: center;
}
</style>