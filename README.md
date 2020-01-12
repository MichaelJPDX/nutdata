# food - Quick & dirty interface to FDA's Nutrition Data API

This project is not intended for public consumption. It's really just a proof-of-concept test to see how the API works for retrieving data.

## Project setup
```
npm install
```
After install, you will  need to create/edit .env.local to add your API key. Obtain a key from https://fdc.nal.usda.gov/
Set the key in .env.local using the line:
```
VUE_APP_API_KEY=YOUR_API_KEY
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Demo

A live demo can be found at: http://travelwebowners.org/nutrition/

## Next steps

Partly done: Read & utilize conversion attributes of the food details to display more meaningful data (tsp of vanilla, for example)
Turns out the foodPortions object has a rather variable structure, so that the info needed, such as "1 cup" or "tsp" may occur in different attributes of the object. Will have to add more code to probe each object for the needed info.

Format similar to standard nutrition facts labeling - requires plucking out specific values from the nutrition data. This is partially done. Still more formatting to do and have to figure out what to do with vitamins, minerals and other misc data. Will probably decide what's useful based on RDAs and discard the rest.
