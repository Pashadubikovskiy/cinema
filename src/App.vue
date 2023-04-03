<template>
  <v-app>
    <v-container>
      <Navbar @searchMovies="searchMovies" @searchByGenre="searchByGenre" />
      <div class="movies-list">
        <Movie
            v-for="movie in movies"
            :key="movie.id"
            :movieInfo="movie"
        />
      </div>
    </v-container>
  </v-app>
</template>

<script>
import Navbar from './components/Navbar';
import Movie from "@/components/Movie";

export default {
  name: 'App',
  components: {
    Navbar,
    Movie
  },
  data: ()=>({
    movies: []
  }),
  mounted() {
    this.getAllMovies()
  },
  methods: {
    searchMovies(text) {
      this.axios.get(`https://cinema-api-test.y-media.io/v1/movies?name=${text}`).then(response => {
        this.movies = response.data.data;
      })
      .catch(function (error) {
        alert(error);
      })
    },
    searchByGenre(genre) {
      this.axios.get(`https://cinema-api-test.y-media.io/v1/movies?genres=${genre}`).then(response => {
        this.movies = response.data.data;
      })
      .catch(function (error) {
        alert(error);
      })
    },
    getAllMovies() {
      this.axios.get('https://cinema-api-test.y-media.io/v1/movies').then(response => {
        this.movies = response.data.data;
        return this.movies;
      })
      .catch(function (error) {
        alert(error);
      })
    },
  }
};
</script>

<style lang="scss">
  #app {
    background: #161212;
    color: #fff;
  }
  .movies-list {
    padding-top: 40px;
  }
</style>