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
                    v-model="searchTerm" v-on:keyup.enter="goSearch"
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
                <v-row align="center">
                  <v-col class="d-flex text-left" cols="12" sm="6">
                      <p>The information below is based on a {{ currentconv.modifier }} amount. To change it, use the drop-down list:</p>
                  </v-col>
                  <v-col class="d-flex" cols="12" sm="6">
                    <v-select
                      :items="conversion"
                      label="Portion Sizes" :return-object="true"
                      item-text="modifier" item-value="id"
                      v-model="currentconv"
                    ></v-select>
                  </v-col>
                </v-row>
                <v-simple-table>
                    <tbody>
                        <tr><td class="text-left"><h2>Calories</h2></td>
                            <td class="text-right"><h2>{{ cals }}</h2></td>
                        </tr>
                        <tr><td></td>
                            <td class="text-right">Amount</td>
                        </tr>
                        <tr><td class="text-left"><strong>Total Fat</strong> {{ Math.round(fat*(currentconv.gramWeight/100)) }}g</td>
                            <td class="text-right">{{ Math.round((fat*(currentconv.gramWeight/100))/78*100) }}%</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Saturated Fat {{ Math.round(satfat*(currentconv.gramWeight/100)) }}g</td>
                            <td class="text-right">{{ Math.round((satfat*(currentconv.gramWeight/100))/20*100) }}%</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Trans Fat {{ Math.round(trans*(currentconv.gramWeight/100)) }}g</td>
                            <td class="text-right"></td>
                        </tr>
                        <tr><td class="text-left"><strong>Cholesterol</strong> {{ Math.round(chol*(currentconv.gramWeight/100)) }}mg</td>
                            <td class="text-right">{{ Math.round((chol*(currentconv.gramWeight/100))/300*100) }}%</td>
                        </tr>
                        <tr><td class="text-left"><strong>Sodium</strong> {{ Math.round(salt*(currentconv.gramWeight/100)) }}mg</td>
                            <td class="text-right">{{ Math.round((salt*(currentconv.gramWeight/100))/2300*100) }}%</td>
                        </tr>
                        <tr><td class="text-left"><strong>Total Carbohydrate</strong> {{ Math.round(carb*(currentconv.gramWeight/100)) }}g</td>
                            <td class="text-right">{{ Math.round((carb*(currentconv.gramWeight/100))/275*100) }}%</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Dietary Fiber {{ Math.round(fiber*(currentconv.gramWeight/100)) }}g</td>
                            <td class="text-right">{{ Math.round((fiber*(currentconv.gramWeight/100))/28*100) }}%</td>
                        </tr>
                        <tr><td class="text-left">&nbsp; Total Sugars {{ Math.round(sugar*(currentconv.gramWeight/100)) }}g</td>
                            <td class="text-right">{{ Math.round((sugar*(currentconv.gramWeight/100))/50*100) }}%</td>
                        </tr>
                        <tr><td class="text-left"><strong>Protein</strong> {{ Math.round(protein*(currentconv.gramWeight/100)) }}g</td>
                            <td class="text-right">{{ Math.round((protein*(currentconv.gramWeight/100))/50*100) }}%</td>
                        </tr>
                    </tbody>
                </v-simple-table>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Vitamin / Mineral</th>
                      <th class="text-left">Amount</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in vitamins" :key="item.id">
                      <td class="text-left">{{ item.nutrient.name }}</td>
                      <td class="text-left">{{ Math.round(item.amount*(currentconv.gramWeight/100)) }}{{ item.nutrient.unitName }}</td>
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
    showDetails: false,
    vitamins: [],
    conversion: [
        { id: 1, gramWeight: 100, modifier: "100 grams" }
    ],
    currentconv: { id: 1, gramWeight: 100, modifier: "100 grams" },
    factor: 100,
    portion: "100 grams",
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
      this.ingredients = ''
      this.currentconv = { id: 1, gramWeight: 100, modifier: "100 grams" }
      this.conversion = [{ id: 1, gramWeight: 100, modifier: "100 grams" }]
      axios.get('https://api.nal.usda.gov/fdc/v1/' + id + '?api_key=' + process.env.VUE_APP_API_KEY)
        .then(response => {
          this.nutrition = response.data.foodNutrients
          for (var i = 0; i < this.nutrition.length; i++) {
              // eslint-disable-next-line
              const tmpVal = nutrients.find(nutrient => nutrient.id == this.nutrition[i].nutrient.number)
              if (tmpVal) {
                  this[tmpVal.field] = this.nutrition[i].amount
              } else {
                  if (this.nutrition[i].amount > 0) { this.vitamins.push(this.nutrition[i]) }
              }
          }
          if (response.data.ingredients) { this.ingredients = response.data.ingredients }
          if (response.data.foodPortions.length > 0) {
              for (var j = 0; j < response.data.foodPortions.length; j++) {
                  this.conversion.push(response.data.foodPortions[j])
              }
          }
          
          this.showResults = false
          this.showDetails = true
          // eslint-disable-next-line
          console.log(this.conversion)
      })
    }
  }
};
</script>
