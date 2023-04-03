<template>
  <div class="movie">
    <img
        :src="movieInfo.image"
        :alt="movieInfo.name"
        class="movie__image"
    >
    <div class="movie__content">
      <div class="movie__name">
        {{movieInfo.name}}
      </div>
      <div class="movie__info" v-html="movieInfo.additional"></div>
      <div class="movie__description" v-html="movieInfo.description"></div>
      <v-btn
          class="movie__btn"
          @click="movieShows(movieInfo.id)"
      >
        Сеанси
      </v-btn>
      <ul
          class="movie__shows movie-shows"
          v-if="showsList.length"
      >
        <li
            class="movie-shows__item"
            v-for="date in showsList"
            :key="`${movieInfo.id}-${date}`"
        >
          <div class="movie-shows__date">
            {{date.showdate}}
          </div>
          <div class="movie-shows__sessions">
            <v-btn
                class="movie-shows__session"
                v-for="session in date.daytime.split(';')"
                :key="`${movieInfo.id}-${date}-${session}`"
                @click="showFreePlaces(movieInfo.id, session, date.showdate)"
            >
              {{session}}
            </v-btn>
          </div>
        </li>
      </ul>
      <div
          class="seats"
          v-if="sessionSeats.length"
      >
        <button
            class="seats__close"
            @click="sessionSeats = []"
        ></button>
        <div class="seats__list seats-list">
          <div
              class="seats-list__line"
              v-for="item in sessionSeats"
              :key="`${activeSession.movie_id}-${item[0].row}`"
          >
            <div class="seats-list__row">
              {{ item[0].row }}
            </div>
            <div class="seats-list__places">
              <v-btn
                  v-for="seat in item[1]"
                  :key="`${activeSession.movie_id}-${seat.seat}-${item[0].row}`"
                  x-small
                  class="seat"
                  color="primary"
                  :disabled="!seat.is_free"
                  @click="checkThisSeat(activeSession.movie_id, item[0].row, seat.seat, activeSession.daytime, activeSession.showdate)"
              >
                {{seat.seat}}
              </v-btn>
            </div>
            <div class="seats-list__row">
              {{ item[0].row }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- for success  -->
    <v-dialog
        v-model="dialog"
        max-width="350"
    >
      <v-card>
        <v-card-title class="text-h5">
          Ви успішно купили квиток
        </v-card-title>

        <v-card-text>
          Ваш номер квитка: {{ticketNumber}}
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn
              color="green darken-1"
              text
              @click="dialog = false"
          >
            Дякую
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data: ()=>({
    showsList: [],
    sessionSeats: [],
    activeSession: {},
    dialog: false,
    ticketNumber: ''
  }),
  props: {
    movieInfo: {
      type: Object,
      required: true,
    },
  },
  methods: {
    movieShows(id) {
      this.axios.get(`https://cinema-api-test.y-media.io/v1/movieShows?movie_id=${id}`).then(response => {
        this.showsList = response.data.data[id];
      })
      .catch(function (error) {
        alert(error);
      });
    },
    showFreePlaces(movie_id = this.activeSession.movie_id, daytime = this.activeSession.daytime, showdate = this.activeSession.showdate) {
      this.axios.get(`https://cinema-api-test.y-media.io/v1/showPlaces?movie_id=${movie_id}&daytime=${daytime}&showdate=${showdate}`).then(response => {
        this.sessionSeats = response.data.data;
        this.activeSession = {
          'movie_id' : movie_id,
          'daytime': daytime,
          'showdate': showdate
        }
      })
      .catch(function (error) {
        alert(error);
      });
    },
    sendInfoToTelegram() {
      let key = '5922207835:AAGx9CQJQS3QIFybHXGWkv2AxovNyBKolHc';

      this.axios.post(`https://api.telegram.org/bot${key}/sendMessage`, {
        chat_id:'372723518',
        text: 'Купили квиточок в кінотеатрі'
      })
      .catch(function (error) {
        console.log(error);
      });
    },
    checkThisSeat(movie_id, row, seat, daytime, showdate) {
      this.axios.post(`https://cinema-api-test.y-media.io/v1/bookPlace`, {
            "movie_id": movie_id,
            "row": row,
            "seat": seat,
            "showdate": showdate,
            "daytime": daytime
          }
      ).then(response => {
        this.ticketNumber = response.data.data.ticketkey;
        this.dialog = true;
        this.showFreePlaces();
        this.sendInfoToTelegram();
      })
      .catch(function (error) {
        alert(error);
      });
    }
  }
}
</script>

<style scoped lang="scss">
  @import "../scss/movie.scss";
</style>