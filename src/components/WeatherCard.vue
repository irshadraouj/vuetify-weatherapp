<template>
  <v-card>
    <!-- Voeg hier je inhoud toe -->
    <v-card-title>{{ weather.name }}</v-card-title>
    <v-card-text>
      <div>{{ weather.main.temp }}°C</div>
      <div>{{ weather.weather[0].description }}</div>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  name: "WeatherCard",
  props: {
    weather: {
      type: Object,
      required: true,
    },
  },
};
</script>
