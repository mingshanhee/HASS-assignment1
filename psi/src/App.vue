<template>
  <div v-if="data == null">Loading...</div>
  <div class="container" v-else>
    <h2>Singapore's Pollutant Standards Index (PSI)</h2>
    <h5>
      The dataset can be obtained from
      <a
        href="https://data.gov.sg/dataset/psi?view_id=496c77eb-6add-4e9b-9883-17864cedfe9f&resource_id=82776919-0de1-4faf-bd9e-9c997f9a729d"
        >data.gov.sg</a
      >
    </h5>
    <p class="mt-4 mb-4">
      <b>Timestamp</b> {{ this.items.timestamp }}<br />
      <b>Updated Timestamp</b>: {{ this.items.update_timestamp }}
    </p>
    <table class="table table-dark table-striped">
      <thead>
        <tr>
          <td>
            <i>Metrics</i>
          </td>
          <td v-for="r in this.regionsCap" v-bind:key="r">
            {{ r }}
          </td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(v, k) in this.items.readings" v-bind:key="k">
          <td>{{ k }}</td>
          <td v-for="r in this.regions" v-bind:key="r">
            {{ v[r] }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      data: null,
      items: null,
      regions: ["west", "national", "east", "central", "north", "south"],
    };
  },
  created() {
    this.fetchPSI();
  },
  computed: {
    regionsCap: function () {
      const regionsCaps = [];
      for (let i = 0; i < this.regions.length; i++) {
        const reg = this.regions[i];
        regionsCaps.push(this.capitalize(reg));
      }
      return regionsCaps;
    },
  },
  methods: {
    async fetchPSI() {
      let response = await fetch("https://api.data.gov.sg/v1/environment/psi");

      console.log(response.status); // 200
      console.log(response.statusText); // OK

      if (response.status === 200) {
        this.data = JSON.parse(await response.text());
        this.items = this.data.items[0];
      }
    },
    capitalize(str) {
      return str[0].toUpperCase() + str.slice(1).toLowerCase();
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
