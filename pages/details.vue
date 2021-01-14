<template>
  <v-container fluid class="pa-5">
    <v-row>
      <v-col>
        <v-btn class="element txt--text" v-on:click="goBack()">Back</v-btn>
      </v-col>
    </v-row>
    <v-row no-gutters class="mt-4">
      <v-col cols="12" md="6">
        <v-container class="container pa-0" fluid>
          <v-img v-bind:src="countryDetails.flag">
            <template v-slot:placeholder>
              <v-row class="fill-height ma-0" align="center" justify="center">
                <v-progress-circular
                  indeterminate
                  color="grey lighten-5"
                ></v-progress-circular>
              </v-row>
            </template>
          </v-img>
        </v-container>
      </v-col>
      <v-col cols="12" md=6>
        <v-container fluid class="pa-4">
          <h3>{{ countryDetails.name }}</h3>
          <br />
          <p><strong>Native Name:</strong> {{ countryDetails.nativeName }}</p>
          <p>
            <strong> Population:</strong>
            {{ formatNumber(countryDetails.population) }}
          </p>
          <p><strong> Region:</strong> {{ countryDetails.region }}</p>
          <p><strong> Sub Region:</strong> {{ countryDetails.subregion }}</p>
          <p><strong> Capital:</strong> {{ countryDetails.capital }}</p>
          <br />
          <p>
            <strong> Top Level Domain: </strong>
            {{ countryDetails.topLevelDomain[0] }}
          </p>
          <span
            v-for="currency in countryDetails.currencies"
            :key="currency.name"
          >
            <p><strong> Currencies: </strong>{{ currency.name }}</p>
          </span>
          <p><strong> Languanges:</strong> {{ getLanguages() }}</p>
          <br />
          <p v-if="countryDetails.borders.length > 0">
            <strong> Border Countries</strong>
          </p>
          <v-btn
            class="ma-1 element txt--text"
            v-for="(border, index) in countryDetails.borders"
            :key="`border-${index}`"
            v-on:click="goToBorderDetails(border)"
            >{{ border }}</v-btn
          >
        </v-container>
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
      countryDetails: null,
      countries: [],
    }
  },
  methods: {
    formatNumber(number) {
      return numeral(number).format()
    },
    getLanguages() {
      const { languages } = this.countryDetails

      let langString = ''

      for (let i = 0; i < languages.length; i++) {
        if (i < languages.length - 1) {
          langString = langString + languages[i].name + ', '
        } else if (i === languages.length - 1) {
          langString = langString + languages[i].name
        }
      }

      return langString
    },
    goToBorderDetails(countryCode) {
      this.$router.push({
        name: 'details',
        query: { code: countryCode.toLowerCase() },
        shallow: false,
      })
    },
    goBack() {
      this.$router.push('/')
    },
    async refreshData() {
      const { $axios, $route: { query: { code } = {} } = {} } = this

      this.countryDetails = await $axios.$get(
        `https://restcountries.eu/rest/v2/alpha/${code}`
      )
    },
  },
  async asyncData(props) {
    const { $axios, route: { query: { code } = {} } = {} } = props

    const countries = await $axios.$get('https://restcountries.eu/rest/v2/all')
    const countryDetails = await $axios.$get(
      `https://restcountries.eu/rest/v2/alpha/${code}`
    )

    return { countries, countryDetails }
  },
  watch: {
    '$route.query.code'() {
        this.$router.go()
    //   this.refreshData()
    //   window.scrollTo(0, 0)
    },
  },
}
</script>
