<template>
  <div class="container" v-if="Object.entries(weatherCity).length">
    <div class="btn-group d-block mt-5 mb-3 text-left" role="group">
      <button v-for="(city, i) in cities" :key="i"
              class="btn btn-secondary btn-sm"
              @click="getWeather(city)">{{city}}</button>
      <button type="button" @click="onchangeMetric"
              class="btn btn-info btn-sm ml-5">{{ metric === 'metric' ? 'F' : 'C' }}</button>
    </div>
    <table class="table">
      <thead>
      <tr>
        <th colspan="2">{{ weatherCity.name }}</th>
      </tr>
      </thead>
      <tbody>
        <tr v-for="(field, i) in showData" :key="i">
          <th>{{ field.label }}</th>
          <th>
            {{ weatherCity[field.data[0]][field.data[1]] }}
            {{ metric === 'metric' ? field.unit[0] :
            (field.unit[1] ? field.unit[1] : field.unit[0]) }}
          </th>
        </tr>
      </tbody>
    </table>

  </div>
</template>

<script>

export default {
  name: 'Home',
  data() {
    return {
      weatherCity: {},
      cities: ['Kyiv', 'London', 'New York'],
      metric: 'metric',
      showData: [
        {
          label: 'Temperature',
          data: ['main', 'temp'],
          unit: ['C', 'F'],
        },
        {
          label: 'Atmospheric pressure',
          data: ['main', 'pressure'],
          unit: ['hPa'],
        },
        {
          label: 'Humidity',
          data: ['main', 'humidity'],
          unit: ['%'],
        },
        {
          label: 'Wind speed',
          data: ['wind', 'speed'],
          unit: ['meter/sec'],
        },
        {
          label: 'Wind direction',
          data: ['wind', 'deg'],
          unit: ['°'],
        },
      ],
    };
  },
  mounted() {
    this.getWeather();
  },
  methods: {
    async getWeather(city = 'Kiev') {
      const baseUrl = 'http://api.openweathermap.org/data/2.5/weather';
      try {
        const data = await this.$axios.get(`${baseUrl}?q=${city}&units=${this.metric}&appid=${process.env.VUE_APP_KEY}`);
        if (data.status === 200) {
          this.weatherCity = data.data;
        }
      } catch (e) {
        console.error(e);
      }
    },
    onchangeMetric() {
      if (this.metric === 'metric') {
        this.metric = 'imperial';
      } else {
        this.metric = 'metric';
      }
      this.getWeather();
    },
  },
};
</script>

<style scoped>
  table th{
    text-align: left;
  }
</style>
