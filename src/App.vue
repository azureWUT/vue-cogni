<template>
  <div id="app">
    <div class="w-full bg-blue-500 p-2 items-center">
      <div class="items-center text-3xl font-semibold flex justify-center py-2">
        Semantive on Azure Search <img class="w-10 h-10 ml-2" src="favicon.ico" alt="">
      </div>
    </div>
    <div class="mx-4 my-4 flex items-center justify-center">
      <div class="font-semibold text-center">
        Type your query:
      </div>
      <div class="mx-4">
        <input type="text" v-model="query"
               class="bg-gray-100 appearance-none border-2 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500"/>
      </div>
      <div class="mx-4">
        <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" @click="getData">Run
          query
        </button>
      </div>
    </div>

    <div class="my-2 text-red-500 font-semibold text-center" v-if="message">{{message}}</div>

    <div v-for="(v, index) in values" :key="v.metadata_storage_path">
      <div class="bg-gray-200 p-4">
        <strong>{{ index + 1 }}. {{ v.metadata_storage_name }}</strong> [score: {{v["@search.score"]}}]
      </div>
      <div class="bg-gray-100 p-4">
        <div class="mx-2">
          <div class="mb-2">
            <div class="font-semibold">
              Content
            </div>
            <div>
              {{ truncate(v.content, 1000) }}
            </div>
          </div>
          <hr>
          <div class="mb-2">
            <div class="font-semibold">
              Organizations
            </div>
            <div class="my-2 text-red-500 font-semibold" v-if="v.organizations.length === 0">No bindings found:(</div>
            <div class="linkinp flex flex-wrap" v-else>
              <a
                  class="button-link"
                  v-for="o in v.organizations"
                  :key="o"
                  @click="
            query = o;
            getData();
          "
              >{{ o }}</a
              >
            </div>
          </div>
          <hr>
          <div class="mb-2">
            <div class="font-semibold">
              Locations
            </div>
            <div class="my-2 text-red-500 font-semibold" v-if="v.locations.length === 0">No bindings found:(</div>
            <div class="linkinp flex flex-wrap" v-else>
              <a
                  class="button-link"
                  v-for="o in v.locations"
                  :key="o"
                  @click="
            query = o;
            getData();
          "
              >{{ o }}</a
              >
            </div>
          </div>
          <hr class="text-xl">
          <div class="mb-2">
            <div class="font-semibold">
              People
            </div>
            <div class="my-2 text-red-500 font-semibold" v-if="v.people.length === 0">No bindings found:(</div>
            <div class="linkinp flex flex-wrap" v-else>
              <a
                  class="button-link"
                  v-for="o in v.people"
                  :key="o"
                  @click="
            query = o;
            getData();
          "
              >{{ o }}</a
              >
            </div>
          </div>
          <hr>
          <div class="mb-2">
            <div class="font-semibold">
              Keyphrases
            </div>
            <div class="my-2 text-red-500 font-semibold" v-if="v.keyphrases.length === 0">No bindings found:(</div>
            <div class="linkinp flex flex-wrap" v-else>
              <a
                  class="button-link"
                  v-for="o in v.keyphrases"
                  :key="o"
                  @click="
            query = o;
            getData();
          "
              >{{ o }}</a
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const index = "search-semantive-index"
const url =
    `https://search-semantive.search.windows.net/indexes/${index}/docs/search?api-version=2016-09-01`;
const apiKey = "73AC349FB3ACBFF6468490EFC3A57C8F";

export default {
  name: "App",
  data() {
    return {
      values: [],
      query: "*",
      message: "",
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
      this.message = "";
      window.scrollTo({ top: 0, behavior: 'smooth' });
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
      if (this.values.length === 0) this.message = "No results :("
      console.log(Object.keys(this.values[0]));
    },
    truncate(str, n) {
      return str.length > n ? str.substr(0, n - 1) + "..." : str;
    },
  },
};
</script>

<style>
.linkinp > a:hover {
  cursor: pointer;
  color: lightgray;
}
</style>
