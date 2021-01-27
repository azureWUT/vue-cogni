<template>
  <div id="app">
    <div>
      <input type="text" v-model="query" placeholder="Give yer query" />
    </div>
    <div>
      <button @click="getData">Run query</button>
    </div>

    <div v-for="v in values" :key="v.metadata_storage_path">
      <h1>{{ v.metadata_storage_name }}</h1>
      <h3>Content</h3>
      <p>
        {{ truncate(v.content, 500) }}
      </p>

      <h3>Organizations</h3>
      <p class="linkinp">
        <a v-for="o in v.organizations" :key="o" @click="query = o; getData()">{{o}}</a>
      </p>

      <h3>Locations</h3>
      <p class="linkinp">
        <a v-for="o in v.locations" :key="o" @click="query = o; getData()">{{o}}</a>
      </p>

      <h3>People</h3>
      <p class="linkinp">
        <a v-for="o in v.people" :key="o" @click="query = o; getData()">{{o}}</a>
      </p>

      <h3>Keyphrases</h3>
      <p class="linkinp">
        <a v-for="o in v.keyphrases" :key="o" @click="query = o; getData()">{{o}}</a>
      </p>
    </div>
  </div>
</template>

<script>
const url = "https://search-semantive.search.windows.net/indexes/azureblob-index/docs/search?api-version=2016-09-01";
const apiKey = "73AC349FB3ACBFF6468490EFC3A57C8F";

export default {
  name: "App",
  data() {
    return {
      values: [],
      query: "*",
    };
  },
  computed: {
    queryVal: function () {
      return this.query || "*";
    },
  },
  methods: {
    async getData(event) {
      if (event) event.preventDefault();
      const response = await fetch(url, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "api-key": apiKey,
        },
        body: JSON.stringify({
          count: true,
          skip: 0,
          top: 50,
          searchMode: "any",
          queryType: "simple",
          search: this.queryVal,
        }),
      });

      const d = await response.json();
      this.values = d.value;
      console.log(Object.keys(this.values[0]))
    },
    truncate(str, n) {
      return str.length > n ? str.substr(0, n - 1) + "..." : str;
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

.linkinp > a:hover { 
  cursor: pointer;
  color: red;
}

.linkinp > a:not(:last-child):after { 
  content: ', '
}
</style>
