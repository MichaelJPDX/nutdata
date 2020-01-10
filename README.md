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

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

## Next steps

Read & utilize conversion attributes of the food details to display more meaningful data (tsp of vanilla, for example)

Format similar to standard nutrition facts labeling - requires plucking out specific values from the nutrition data.
