<template>
  <v-app>
    <v-app-bar class="input pa-4">
      <v-autocomplete
        v-model="selectedMovie"
        v-model:search="search"
        :items="entries"
        :loading="isLoading"
        :hide-no-data="isLoading || search.length === 0"
        item-title="Title"
        item-value="Title"
        auto-select-first
        clearable
        prepend-inner-icon="mdi-magnify"
        placeholder="Enter movie name"
        return-object
      >
        <template v-slot:no-data>No results found</template>
      </v-autocomplete>
    </v-app-bar>

    <div class="container">
      <section class="movie__list pt-16 ma-8">
        <div v-for="movie in entries" :key="movie.imdbID" class="ma-2">
          <a
            :href="'https://www.imdb.com/title/' + movie.imdbID"
            style="text-decoration: none"
          >
            <v-card width="350">
              <v-img
                :src="movie.Poster"
                height="400px"
                class="pa-4"
                cover
              ></v-img>

              <v-card-title class="pt-4 pb-4">
                {{ movie.Title }}
              </v-card-title>
              <v-card-subtitle class="pb-4">
                Year: {{ movie.Year }}</v-card-subtitle
              >
              <v-card-subtitle class="pb-4">
                imdbID: {{ movie.imdbID }}</v-card-subtitle
              >
            </v-card>
          </a>
        </div>
      </section>
    </div>
  </v-app>
</template>

<script>
import _ from "lodash";
export default {
  data: () => ({
    entries: [],
    isLoading: false,
    selectedMovie: null,
    search: "",
  }),
  mounted() {
    this.fetchMovies("sun");
  },
  watch: {
    search(val) {
      this.isLoading = true;
      return this.fetchMovies(val);
    },
  },
  methods: {
    fetchMovies: _.debounce(function (val) {
      fetch(
        `https://www.omdbapi.com/?` +
          new URLSearchParams({
            apiKey: "62fc2f6c",
            s: val.toLowerCase(),
            type: "movie",
            r: "json",
          })
      )
        .then((res) => res.json())
        .then((res) => {
          const { Search = [] } = res;
          this.entries = Search;
          console.log(this.entries);
        })
        .catch((err) => {
          console.log(err);
        })
        .finally(() => (this.isLoading = false));
    }, 500),
  },
};
</script>

<style>
.input {
  padding: 10px;
  height: 78px;
  box-shadow: 0 2px 4px -1px rgb(0 0 0 / 20%), 0 4px 5px 0 rgb(0 0 0 / 14%),
    0 1px 10px 0 rgb(0 0 0 / 12%);
}
.contain {
  margin: 0 auto;
}

.movie__list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin: 16px;
}
</style>
