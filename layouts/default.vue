<template>
  <v-app>
    <v-app-bar dense flat app class="element">
      <h4 class="txt--text">Where in the world?</h4>
      <v-spacer></v-spacer>
      <v-btn icon v-on:click="toggleNightMode">
        <v-icon>mdi-white-balance-sunny</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main class="background"> 
      <v-container fluid class="pa-0">
        <nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {

    }
  },
  methods: {
    toggleNightMode() {
        this.$vuetify.theme.dark = !this.$vuetify.theme.dark;
        localStorage.setItem("isDarkMode", this.$vuetify.theme.isDark.toString());
    }
  },
  mounted() {
    this.$meta().refresh()
    const isDarkMode = localStorage.getItem("isDarkMode");
    if (isDarkMode) {
        if (isDarkMode === "true") {
            this.$vuetify.theme.dark = true;
        } else {
            this.$vuetify.theme.dark = false;
        }
    } 
    else if (
        window.matchMedia &&
        window.matchMedia("(prefers-color-scheme: dark)").matches
    ) {
        this.$vuetify.theme.dark = true;
        localStorage.setItem(
            "isDarkMode",
            this.$vuetify.theme.dark.toString()
        );
    }
  },
}
</script>
