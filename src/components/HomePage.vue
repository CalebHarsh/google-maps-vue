<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <GoogleMaps v-bind:locations="properties" />
  </div>
</template>

<script>
import propertyData from "../assets/property_data.json";
import GoogleMaps from "./GoogleMaps.vue";

export default {
  data() {
    return {
      data: propertyData,
      filter: false,
    };
  },
  name: "HomePage",
  props: {
    msg: String
  },
  components: {
    GoogleMaps
  },
  computed: {
    properties: function () {
      const sortedData = this.data.slice().sort((x, y) => {
        return parseInt(x.ESTIMATED_MARKET_VALUE) - parseInt(y.ESTIMATED_MARKET_VALUE);
      });
      // if (this.filter) {
      //   return this.sortedData.filter(elem => {
      //     return true;
      //   });
      // }
      return sortedData;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
