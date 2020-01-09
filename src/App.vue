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
                <h1>Nutrition Facts</h1>
                <v-simple-table>
                    <tbody>
                        <tr><td class="text-left"><h2>Calories</h2></td>
                            <td class="text-right"><h2>{{ cals }}</h2></td>
                        </tr>
                        <tr><td></td>
                            <td class="text-right">Amount</td>
                        </tr>
                        <tr><td class="text-left"><strong>Total Fat</strong></td>
                            <td class="text-right">{{ fat }}g</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Saturated Fat</td>
                            <td class="text-right">{{ satfat }}g</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Trans Fat</td>
                            <td class="text-right">{{ trans }}g</td>
                        </tr>
                        <tr><td class="text-left"><strong>Cholesterol</strong></td>
                            <td class="text-right">{{ chol }}mg</td>
                        </tr>
                        <tr><td class="text-left"><strong>Sodium</strong></td>
                            <td class="text-right">{{ salt }}mg</td>
                        </tr>
                        <tr><td class="text-left"><strong>Total Carbohydrate</strong></td>
                            <td class="text-right">{{ carb }}g</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Dietary Fiber</td>
                            <td class="text-right">{{ fiber }}g</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Total Sugars</td>
                            <td class="text-right">{{ sugar }}g</td>
                        </tr>
                        <tr><td class="text-left"><strong>Protein</strong></td>
                            <td class="text-right">{{ protein }}g</td>
                        </tr>
                    </tbody>
                </v-simple-table>
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
                <p>Calories: {{ cals }}</p>
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
    showDetails: false,
    protein: 0,
    fat: 0,
    carb: 0,
    ash: 0,
    cals: 0,
    water: 0,
    sugar: 0,
    fiber: 0,
    chol: 0,
    trans: 0,
    satfat: 0,
    monofat: 0,
    polyfat: 0,
    salt: 0
  }),
  methods: {
    goSearch() {
        const query = {"generalSearchInput":this.searchTerm, "requireAllWords":true}
        axios.post('https://api.nal.usda.gov/fdc/v1/search?api_key=' + process.env.VUE_APP_API_KEY, query)
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
      const nutrients = [
            { id: 203, field: 'protein' },
            { id: 204, field: 'fat' },
            { id: 205, field: 'carb' },
            { id: 207, field: 'ash' },
            { id: 208, field: 'cals' },
            { id: 255, field: 'water' },
            { id: 269, field: 'sugar' },
            { id: 291, field: 'fiber' },
            { id: 307, field: 'salt' },
            { id: 601, field: 'chol' },
            { id: 605, field: 'trans' },
            { id: 606, field: 'satfat' },
            { id: 645, field: 'monofat' },
            { id: 646, field: 'polyfat' }
        ]
      axios.get('https://api.nal.usda.gov/fdc/v1/' + id + '?api_key=' + process.env.VUE_APP_API_KEY)
        .then(response => {
          this.nutrition = response.data.foodNutrients
          for (var i = 0; i < this.nutrition.length; i++) {
              // eslint-disable-next-line
              const tmpVal = nutrients.find(nutrient => nutrient.id == this.nutrition[i].nutrient.number)
              if (tmpVal) {
                  this[tmpVal.field] = this.nutrition[i].amount
              }
          }
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
