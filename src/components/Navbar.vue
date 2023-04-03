<template>
  <v-container class="navbar">
    <v-row>
      <v-col
          cols="12"
          sm="6"
          md="3"
      >
        <v-text-field
            label="Search"
            v-model="searchText"
            @input="searchMovies"
            class="navbar__search"
        ></v-text-field>
      </v-col>
      <v-col
          cols="12"
          sm="6"
          md="3"
      >
        <v-select
            v-model="searchGenre"
            @change="searchGenres()"
            class="navbar__category"
            :items="items"
            item-text="genre"
            item-value="id"
            label="Choose genre"
        >
        </v-select>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    searchText: '',
    searchGenre: '',
    items: [
      { genre: 'All', id: '' },
      { genre: 'ACTION', id: '0' },
      { genre: 'ADVENTURES', id: '1' },
      { genre: 'COMEDY', id: '2' },
      { genre: 'DRAMA', id: '3' },
      { genre: 'HORROR', id: '4' },
      { genre: 'WESTERNS', id: '5' },
    ],
  }),
  methods: {
    searchMovies() {
      this.searchGenre = '';
      this.$emit('searchMovies', this.searchText)
    },
    searchGenres() {
      this.searchText = '';
      this.$emit('searchByGenre', this.searchGenre)
    }
  }
}
</script>

<style scoped lang="scss">
  .navbar {
    display: flex;
    align-content: center;
    color: #fff;
  }
</style>