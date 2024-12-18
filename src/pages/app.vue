<template>
  <v-app>
    <!-- Header -->
    <v-app-bar color="#102840" app>
      <v-toolbar-title class="header-title">
        Weather App
      </v-toolbar-title>
    </v-app-bar>

    <!-- Main Content -->
    <v-main class="app-background">
      <v-container>
        <!-- Zoekbalk en knop -->
        <v-row justify="center">
          <v-col cols="12" sm="8" md="6" lg="4">
            <v-text-field
              v-model="city"
              label="Enter city name"
              outlined
              dense
              class="search-bar"
            ></v-text-field>
            <v-btn color="#102840" class="mt-2" block @click="fetchWeather">
              Check Weather
            </v-btn>
          </v-col>
        </v-row>

        <!-- Suggested Cities -->
        <v-row justify="center" class="mt-4">
          <v-col cols="12" sm="8" md="6" lg="4">
            <v-btn
              v-for="suggestion in citySuggestions"
              :key="suggestion"
              class="mt-2 suggestion-btn"
              @click="setCity(suggestion)"
              block
            >
              {{ suggestion }}
            </v-btn>
          </v-col>
        </v-row>

        <!-- Filters -->
        <v-row justify="center" class="mt-4" v-if="weatherData">
          <v-col cols="12" sm="8" md="6" lg="4">
            <v-btn
              color="#102840"
              class="mt-2"
              block
              @click="selectAllFilters"
            >
              Select All
            </v-btn>
            <v-select
              v-model="selectedFilters"
              :items="filters"
              label="Select Weather Data"
              multiple
              chips
              clearable
              outlined
              class="filter-select"
            ></v-select>
          </v-col>
        </v-row>

        <!-- Weerkaart met geselecteerde gegevens -->
        <v-row justify="center" v-if="weatherData">
          <v-col cols="12" sm="8" md="6" lg="4">
            <v-card outlined class="weather-card">
              <v-card-title class="justify-center">
                {{ weatherData.name }}, {{ weatherData.sys.country }}
              </v-card-title>
              <v-card-text class="text-center">
                <v-img
                  :src="getWeatherIcon(weatherData.weather[0].icon)"
                  max-height="100"
                  class="mb-3"
                ></v-img>
                <div>
                  <strong>{{ weatherData.main.temp }}°C</strong> - {{
                    weatherData.weather[0].description
                  }}
                </div>
                <div class="mt-2">
                  <strong>Selected Data:</strong>
                  <div v-if="selectedFilters.length">
                    <div v-for="filter in selectedFilters" :key="filter">
                      <p>{{ getSelectedData(filter) }}</p>
                    </div>
                  </div>
                </div>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <!-- Footer -->
    <v-footer color="#102840" dark app>
      <v-col class="text-center white--text">
        © {{ new Date().getFullYear() }} Weather App by Irshad Raouj
        <div class="mt-2">
          <!-- Social Media Links -->
          <v-btn href="https://facebook.com" target="_blank" icon>
            <v-icon color="white">mdi-facebook</v-icon>
          </v-btn>
          <v-btn href="https://instagram.com" target="_blank" icon>
            <v-icon color="white">mdi-instagram</v-icon>
          </v-btn>
          <v-btn href="https://twitter.com" target="_blank" icon>
            <v-icon color="white">mdi-twitter</v-icon>
          </v-btn>
          <v-btn href="https://snapchat.com" target="_blank" icon>
            <v-icon color="white">mdi-snapchat</v-icon>
          </v-btn>
        </div>
      </v-col>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      city: "",
      weatherData: null,
      apiKey: "2662e2a29509784ed9bb3a5b951283dd",
      filters: [
        "Humidity",
        "Max Temp",
        "Min Temp",
        "Precipitation Chance",
        "Wind Direction",
        "Wind Speed",
        "Pressure",
      ],
      selectedFilters: [],
      citySuggestions: ["Amsterdam", "London", "Paris", "New York", "Tokyo"],
    };
  },
  methods: {
    async fetchWeather() {
      if (!this.city) {
        this.$vuetify.toast.error("Please enter a city name");
        return;
      }
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${this.apiKey}&units=metric`;
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error("City not found");
        this.weatherData = await response.json();
      } catch (error) {
        this.$vuetify.toast.error(error.message);
      }
    },
    setCity(city) {
      this.city = city;
      this.fetchWeather();
    },
    selectAllFilters() {
      this.selectedFilters = [...this.filters];
    },
    getWeatherIcon(iconCode) {
      return `http://openweathermap.org/img/wn/${iconCode}@2x.png`;
    },
    getSelectedData(filter) {
      if (!this.weatherData) return null;
      const { main, wind, weather } = this.weatherData;
      switch (filter) {
        case "Humidity":
          return `Humidity: ${main.humidity}%`;
        case "Max Temp":
          return `Max Temp: ${main.temp_max}°C`;
        case "Min Temp":
          return `Min Temp: ${main.temp_min}°C`;
        case "Precipitation Chance":
          return weather[0].description.includes("rain")
            ? "High Chance of Rain"
            : "No Rain Expected";
        case "Wind Direction":
          return `Wind Direction: ${wind.deg}°`;
        case "Wind Speed":
          return `Wind Speed: ${wind.speed} m/s`;
        case "Pressure":
          return `Pressure: ${main.pressure} hPa`;
        default:
          return "Select a valid filter.";
      }
    },
  },
};
</script>

<style scoped>
/* Achtergrondkleur van de app */
.app-background {
  background-color: #7d7878;
  min-height: 100vh;
}

/* Header-tekststijl */
.header-title {
  color: white;
  font-weight: bold;
  font-size: 1.5rem;
}

/* Zoekbalk achtergrondkleur aanpassen */
.search-bar .v-input__control {
  background-color: #102840;
  color: white;
}

/* Zoekbalk label kleur aanpassen */
.search-bar .v-input__label {
  color: white;
}

/* Filters stijl */
.filter-select .v-select__selections {
  background-color: #102840;
  color: white;
}

/* Chips stijl */
.v-chip {
  background-color: #102840;
  color: white;
}

/* Weerkaart styling */
.weather-card {
  background-color: #102840 !important; 
  color: white !important; 
  border-radius: 10px;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
}

/* Stijl van de Check Weather knop */
.v-btn {
  background-color: #102840 !important; 
  color: white !important;
  border-radius: 5px;
  font-weight: bold;
}

/* Suggestieknoppen */
.suggestion-btn {
  background-color: #102840 !important; 
  color: white !important;
  border-radius: 5px;
  font-weight: bold;
  margin-bottom: 10px;
}

/* Footer kleur */
.v-footer {
  background-color: #102840 !important;
}

.v-footer .v-btn {
  margin: 0 5px;
}
</style>



