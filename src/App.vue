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
                            <td class="text-right"><h2>{{ Math.round(cals*(currentconv.gramWeight/100)) }}</h2></td>
                        </tr>
                        <tr><td></td>
                            <td class="text-right">% RV</td>
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
                      <th class="text-right">%DV</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in vitaminerals" :key="item.id">
                      <td class="text-left" v-if="item.amount > 0">{{ item.name }} {{ Math.round(item.amount*(currentconv.gramWeight/100)) }}{{ item.units }}</td>
                      <td class="text-right" v-if="item.amount > 0">{{ Math.round((item.amount*(currentconv.gramWeight/100))/item.dv*100) }}%</td>
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
    vitaminerals: [
        { id: 1, name: "Biotin", dv: 300, amount: 0, units: '' },
        { id: 417, name: "Folic Acid", dv: 400, amount: 0, units: '' },
        { id: 406, name: "Niacin", dv: 20, amount: 0, units: '' },
        { id: 410, name: "Pantothetic Acid", dv: 10, amount: 0, units: '' },
        { id: 405, name: "Riboflavin", dv: 1.7, amount: 0, units: '' },
        { id: 404, name: "Thiamin", dv: 1.5, amount: 0, units: '' },
        { id: 318, name: "Vitamin A", dv: 5000, amount: 0, units: '' },
        { id: 415, name: "Vitamin B-6", dv: 2, amount: 0, units: '' },
        { id: 418, name: "Vitamin B-12", dv: 6, amount: 0, units: '' },
        { id: 401, name: "Vitamin C", dv: 60, amount: 0, units: '' },
        { id: 324, name: "Vitamin D", dv: 400, amount: 0, units: '' },
        { id: 323, name: "Vitamin E", dv: 30, amount: 0, units: '' },
        { id: 430, name: "Vitamin K", dv: 80, amount: 0, units: '' },
        { id: 301, name: "Calcium", dv: 1000, amount: 0, units: '' },
        { id: 15, name: "Chloride", dv: 3400, amount: 0, units: '' },
        { id: 16, name: "Chromium", dv: 120, amount: 0, units: '' },
        { id: 312, name: "Copper", dv: 2, amount: 0, units: '' },
        { id: 17, name: "Iodine", dv: 150, amount: 0, units: '' },
        { id: 303, name: "Iron", dv: 18, amount: 0, units: '' },
        { id: 304, name: "Magnesium", dv: 400, amount: 0, units: '' },
        { id: 315, name: "Manganese", dv: 2, amount: 0, units: '' },
        { id: 22, name: "Molybdenum", dv: 75, amount: 0, units: '' },
        { id: 305, name: "Phosporus", dv: 1000, amount: 0, units: '' },
        { id: 306, name: "Potassium", dv: 3500, amount: 0, units: '' },
        { id: 317, name: "Selenium", dv: 70, amount: 0, units: '' },
        { id: 307, name: "Sodium", dv: 2400, amount: 0, units: '' },
        { id: 309, name: "Zinc", dv: 15, amount: 0, units: '' }
    ],
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
        axios.post('https://api.nal.usda.gov/fdc/v1/foods/search?api_key=' + process.env.VUE_APP_API_KEY, query)
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
      axios.get('https://api.nal.usda.gov/fdc/v1/food/' + id + '?api_key=' + process.env.VUE_APP_API_KEY)
        .then(response => {
          this.nutrition = response.data.foodNutrients
          for (var i = 0; i < this.nutrition.length; i++) {
              // eslint-disable-next-line
              const tmpVal = nutrients.find(nutrient => nutrient.id == this.nutrition[i].nutrient.number)
              if (tmpVal) {
                  this[tmpVal.field] = this.nutrition[i].amount
              } else {
                  // Handle vitamins and minerals
                  if (this.nutrition[i].amount > 0) {
                      const tmpVit = this.vitaminerals.find(nutrient => nutrient.id == this.nutrition[i].nutrient.number)
                      if (tmpVit) {
                          tmpVit.amount = this.nutrition[i].amount
                          tmpVit.units = this.nutrition[i].nutrient.unitName
                      }
                  }
                  //this.vitamins.push(this.nutrition[i])
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
          //console.log(response.data.foodPortions)
      })
    }
  }
};
</script>
