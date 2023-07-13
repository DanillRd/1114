<template>
  <div>
    <Header>Task</Header>
    <main>
      <h1>Варианты доступных карт фильмов</h1>
      <div class="movie-page" @mousemove="handleMouseMove">
        <MovieCard
            v-for="movie in movies"
            :key="movie.id"
            :movie="movie"
            @delete="deleteMovie(movie.id)"
            @click="openMovie(movie.id)"
            :class="{ 'open': movie.id === openMovieId }"
        >
          <template v-slot:actions>
            <button class="button" @click="addToFavorites(movie)">Добавить в избранное</button>
            <button class="button" @click="addToWatchlist(movie)">Добавить в список просмотра</button>
            <button class="button" @click="deleteMovie(movie.id)">Удалить</button>
          </template>
        </MovieCard>
      </div>

      <div class="lists-container">
        <div class="list1">
          <h2>Список избранного</h2>
          <ul>
            <li v-for="favoriteMovie in favoriteMovies" :key="favoriteMovie.id">
              {{ favoriteMovie.title }}
              <button class="delete-button-list" @click="deleteFromList(favoriteMovie.id, 'favoriteMovies')">Удалить</button>
            </li>
          </ul>
        </div>

        <div class="list2">
          <h2>Список просмотра</h2>
          <ul>
            <li v-for="watchlistMovie in watchlistMovies" :key="watchlistMovie.id">
              {{ watchlistMovie.title }}
              <button class="delete-button-list" @click="deleteFromList(watchlistMovie.id, 'watchlistMovies')">Удалить</button>
            </li>
          </ul>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import MovieCard from './MovieCard.vue';
import axios from 'axios';
import Header from "/src/Header.vue";

export default {
  components: {
    Header,
    MovieCard
  },
  data() {
    return {
      movies: [], // Массив фильмов
      favoriteMovies: [], // Список избранного
      watchlistMovies: [], // Список просмотра
      openMovieId: null // Идентификатор текущего открытого фильма
    };
  },
  created() {
    this.loadMovies();
  },
  methods: {
    async loadMovies() {
      try {
        const apiKey = 'e01ffe1c66cbb475893aa1494a0e6834';
        const response = await axios.get(`https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}`);
        this.movies = response.data.results;
      } catch (error) {
        console.error(error);
        this.movies = [];
      }
    },
    addToFavorites(movie) {
      // Логика добавления фильма в избранное
      this.favoriteMovies.push(movie);
    },
    addToWatchlist(movie) {
      // Логика добавления фильма в список просмотра
      this.watchlistMovies.push(movie);
    },
    deleteMovie(movieId) {
      // Логика удаления фильма по идентификатору
      this.movies = this.movies.filter(movie => movie.id !== movieId);
      this.favoriteMovies = this.favoriteMovies.filter(movie => movie.id !== movieId);
      this.watchlistMovies = this.watchlistMovies.filter(movie => movie.id !== movieId);
    },
    deleteFromList(movieId, listName) {
      // Логика удаления фильма из указанного списка
      if (listName === 'favoriteMovies') {
        this.favoriteMovies = this.favoriteMovies.filter(movie => movie.id !== movieId);
      } else if (listName === 'watchlistMovies') {
        this.watchlistMovies = this.watchlistMovies.filter(movie => movie.id !== movieId);
      }
    },
    resetCard() {
      this.openMovieId = null;
      this.$refs.movieCards.forEach(card => card.resetCard());
    },
    openMovie(id) {
      this.openMovieId = id;

      setTimeout(() => {
        this.resetCard();
      }, 15000);
    }
  }
};
</script>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.movie-page {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  max-width: 100%;
  margin: 0 auto;
  padding: 20px;
  box-sizing: border-box;
}

.lists-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  max-width: 100%;
  margin: 0 auto;
  padding: 20px;
  box-sizing: border-box;
}

.list1,
.list2 {
  flex: 1 1 48%;
  margin-bottom: 20px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f5f5f5;
}

.list1 h2,
.list2 h2 {
  color: #333;
  font-size: 18px;
  margin-top: 0;
  margin-bottom: 10px;
}

.list1 ul,
.list2 ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.list1 li,
.list2 li {
  padding: 5px 0;
  border-bottom: 1px solid #ddd;
  color: #678;
}

.list1 li:last-child,
.list2 li:last-child {
  border-bottom: none;
}

.list1 li:hover,
.list2 li:hover {
  background-color: #e0e0e0;
  cursor: pointer;
}

h1 {
  text-align: center;
  margin: 20px 0;
  color: #CD6600;
  font-size: 40px;
}

button.button {
  border-radius: 30px;
  padding: 10px 20px;
  font-size: 18px;
  font-weight: bold;
  background-color: #3f93ff;
  border: none;
  color: white;
  margin-top: 20px;
}

@media (max-width: 768px) {
  .list1,
  .list2 {
    flex-basis: 100%;
  }
}

@media (max-width: 576px) {
  .movie-page,
  .lists-container {
    padding: 10px;
  }
}

@media (max-width: 480px) {
  button.button {
    font-size: 14px;
  }
}
</style>

