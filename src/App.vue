<template>
  <div id="app">
    <input type="text" v-model="filter" placeholder="搜尋">
    <ul>
      <template v-if="filter && filterArry.length">  
        <li v-for="item in filterArry">
          <span v-html="hightLight(item)"></span>
          <span class="population">{{ population(item.population) }}</span>
        </li>
      </template>  
      <template v-else>
        <li>  
          <span>filter city</span>
        </li>
        <li>  
          <span>or state</span>
        </li>
      </template>
    </ul>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";
import axios from "axios";
import _ from "lodash";

export default {
  name: "App",
  data() {
    return {
      cityMap: [],
      filter: ""
    };
  },
  computed: {
    regexp() {
      return new RegExp(this.filter, "gi");
    },
    filterArry() {
      return this.cityMap.filter(city => {
        return city.city.match(this.regexp) || city.state.match(this.regexp);
      });
    }
  },
  components: {
    HelloWorld
  },
  methods: {
    API_city() {
      let url =
        "https://gist.githubusercontent.com/Miserlou/c5cd8364bf9b2420bb29/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json";

      axios.get(url).then(res => {
        this.cityMap = res.data;
      });

      // fetch(url).then(res => {
      //   res.json().then(obj => {
      //     this.cityMap = obj;
      //   });
      // });
    },
    population(num) {
      return Number(num).toLocaleString();
    },
    hightLight(item) {
      let cityName = item.city.replace(
        this.regexp,
        `<span class="hl">${this.filter}</span>`
      );

      let stateName = item.state.replace(
        this.regexp,
        `<span class="hl">${this.filter}</span>`
      );

      return `${cityName}, ${stateName}`;
    },
    debounceTest: _.debounce(function(w) {
      console.log(w);
    }, 500)
  },
  mounted() {
    this.API_city();

    window.onresize = event => {
      this.debounceTest(event.target.innerWidth);
    };
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

ul {
  list-style: none;
  width: 50%;
  margin: 15px auto;
}

li {
  border: 1px solid #ddd;
  border-radius: 10px;
  display: flex;
  justify-content: space-between;
  padding: 10px 15px;
  margin-bottom: 10px;
  color: #555;
}

span.population {
  font-size: 15px;
}

.hl {
  background: #ffc600;
}
</style>
