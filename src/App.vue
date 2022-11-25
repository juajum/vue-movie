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
    <v-container class="mt-16 pt-12" fluid>
      <v-row>
        <v-col
          v-for="movie in entries"
          :key="movie.imdbID"
          cols="12"
          xs="12"
          sm="6"
          md="4"
          lg="3"
        >
          <a
            :href="'https://www.imdb.com/title/' + movie.imdbID"
            style="text-decoration: none"
          >
            <v-card w-100>
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
        </v-col>
      </v-row>
    </v-container>
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
    mobileView: false,
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

    handleView() {
      window.innerWidth <= 428
        ? (this.tabletView = true)
        : (this.tabletView = false);
    },
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
</style>
