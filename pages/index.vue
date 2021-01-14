<template>
  <v-container fluid class="pa-4">
    <v-row>
      <v-col cols="12" sm="8" md="12">
        <v-autocomplete
          class="ma-0 text--input"
          flat
          solo
          placeholder="Enter to search..."
          :items="fixCountries"
          item-text="name"
          clearable
          v-model="selectedLabel"
          @change="filterCountry()"
        >
          <v-icon slot="append"> mdi-magnify </v-icon>
        </v-autocomplete>
      </v-col>
    </v-row>
    <v-row>
      <v-col class="d-flex" cols="12" sm="6" md="12">
        <v-select
          :items="regions"
          label="Filter by region"
          clearable
          v-model="selectedRegion"
          @change="filterCountry()"
          solo
          class="text--input"
        ></v-select>
      </v-col>
    </v-row>
    <v-row justify="center">
      <span v-if="countries.length === 0">No matched results. </span>
      <v-col
        cols="12"
        sm="8"
        md="4"
        v-for="country in countries"
        :key="country.name"
      >
        <v-card v-on:click="goToDetails(country.alpha3Code)" class="element">
          <v-img height="250" v-bind:src="country.flag"></v-img>
          <v-container fluid class="pa-4">
            <h3>{{ country.name }}</h3>
            <br />
            <p>
              <strong> Population:</strong>
              {{ formatNumber(country.population) }}
            </p>
            <p><strong> Region: </strong>{{ country.region }}</p>
            <p><strong> Capital:</strong> {{ country.capital }}</p>
          </v-container>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import numeral from 'numeral'
import { find } from 'lodash'

export default {
  data() {
    return {
      countries: [],
      regions: ['Africa', 'Americas', 'Asia', 'Europe', 'Oceania'],
      selectedLabel: '',
      selectedRegion: '',
    }
  },
  methods: {
    formatNumber(number) {
      return numeral(number).format()
    },
    async filterCountry() {
      let labelFiltered = []
      let regionFiltered = []
      let tempCountries = []

      if (this.selectedLabel !== null && this.selectedLabel !== '') {
        try {
          tempCountries = await this.$axios.$get(
            `https://restcountries.eu/rest/v2/name/${this.selectedLabel}`
          )
        } catch (err) {
          return
        }
      } else {
        tempCountries = this.fixCountries
      }

      if (this.selectedRegion !== null && this.selectedRegion !== '') {
        let tempCountries2 = []
        tempCountries.map((value) => {
          if (value.region === this.selectedRegion) {
            tempCountries2.push(value)
          }
        })

        tempCountries = tempCountries2
      }

      this.countries = tempCountries
    },
    goToDetails(countryCode) {
      this.$router.push({
        name: 'details',
        query: { code: countryCode.toLowerCase() },
      })
    },
  },
  async asyncData({ $axios, $router }) {
    const countries = await $axios.$get('https://restcountries.eu/rest/v2/all')
    const fixCountries = await $axios.$get(
      'https://restcountries.eu/rest/v2/all'
    )
    return { countries, fixCountries }
  },
}
</script>
