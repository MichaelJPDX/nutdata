<template>
  <v-app>
    <v-app-bar
      app
      color="accent"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="http://chiengfa.com/ninei.png"
          transition="scale-transition"
          width="40"
        />

        <v-toolbar-title class="title">Nutrition Facts</v-toolbar-title>
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="http://chiengfa.com"
        target="_blank"
        text
      >
        <span class="mr-2">Portfolio</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content>
      <v-container>
        <v-layout
          text-center
          wrap
        >
            <v-flex xs11>
                <v-text-field
                    label="Search term"
                    placeholder="ex. Broccoli"
                              v-model="searchTerm"
                  ></v-text-field>
            </v-flex>
            <v-flex xs1>
                <v-btn text @click="goSearch"><v-icon>mdi-magnify</v-icon></v-btn>
            </v-flex>
            <v-flex xs12 v-if="showResults">
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Name</th>
                      <th class="text-left">Brand</th>
                      <th>Get Details</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in resultList" :key="item.fdcId">
                      <td class="text-left">{{ item.description }}</td>
                      <td class="text-left">{{ item.brandOwner }}</td>
                      <td><v-btn text @click="getDetails(item.fdcId)"><v-icon>mdi-information-outline</v-icon></v-btn></td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-flex>
            <v-flex xs12 v-if="showDetails">
                <p v-if="ingredients !== ''">Ingredients: {{ ingredients }}</p>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th>ID</th>
                      <th class="text-left">Nutrient</th>
                      <th class="text-left">Amount</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in nutrition" :key="item.id">
                      <td class="text-left">{{ item.nutrient.number }}</td>
                      <td class="text-left">{{ item.nutrient.name }}</td>
                      <td class="text-left">{{ item.amount }}{{ item. nutrient.unitName }}</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-flex>
        </v-layout>
      </v-container>
          
    </v-content>
  </v-app>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',

  components: {
    // HelloWorld,
  },

  data: () => ({
    searchTerm: '',
    resultList: [],
    showResults: false,
    nutrition: [],
    ingredients: '',
    showDetails: false
  }),
  methods: {
    goSearch() {
        const query = {"generalSearchInput":this.searchTerm, "requireAllWords":true}
        axios.post('https://api.nal.usda.gov/fdc/v1/search?api_key=uHsQKXy2poBcjUYiBWVxxXyaJIc5CoQGUvLPi3dU', query)
            .then (response => {
                this.resultList = response.data.foods
                this.showResults = true
                // eslint-disable-next-line
                // console.log(this.resultList)
            })
          .catch(error => {
          // eslint-disable-next-line
            console.log(error)
          // eslint-disable-next-line
            console.log(error.request)
          // eslint-disable-next-line
            console.log(error.data)
          // eslint-disable-next-line
            console.log(error.headers)
        })
    },
    getDetails(id) {
      axios.get('https://api.nal.usda.gov/fdc/v1/' + id + '?api_key=uHsQKXy2poBcjUYiBWVxxXyaJIc5CoQGUvLPi3dU')
        .then(response => {
          this.nutrition = response.data.foodNutrients
          if (response.data.ingredients) { this.ingredients = response.data.ingredients }
          this.showResults = false
          this.showDetails = true
          // eslint-disable-next-line
          console.log(response.data)
      })
    }
  }
};
</script>
