<template>
  <div class="card" @mouseenter="startTimer" @mouseleave="resetTimer">
    <div class="card-inner" :class="{ 'flipped': isFlipped }">
      <div class="card-front" @click="toggleMovie">
        <div class="image-wrapper">
          <img :src="getImageUrl(movie.poster_path)" alt="Movie Poster">
        </div>
      </div>
      <div class="card-back" @click="toggleMovie">
        <div class="content">
          <h2>{{ movie.title }}</h2>
          <p class="overview">{{ truncateText(movie.overview, 150) }}</p>
          <a class="movie-link" :href="'https://www.themoviedb.org/movie/' + movie.id" target="_blank"> Смотреть!</a>
          <slot name="actions"></slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    movie: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      isFlipped: false, // Состояние разворота карты
      timer: null // Таймер для сброса карты
    };
  },
  methods: {
    getImageUrl(posterPath) {
      const baseUrl = 'https://image.tmdb.org/t/p/w500';
      return baseUrl + posterPath;
    },
    toggleMovie() {
      this.isFlipped = !this.isFlipped;
    },
    startTimer() {
      this.timer = setTimeout(() => {
        this.resetCard();
      }, 10000); // 10 секунд
    },
    resetTimer() {
      clearTimeout(this.timer);
    },
    resetCard() {
      this.isFlipped = false;
    },
    truncateText(text, maxLength) {
      if (text.length <= maxLength) {
        return text;
      } else {
        return text.substring(0, maxLength) + '...';
      }
    }
  },
  computed: {
    cardClass() {
      return {
        flipped: this.isFlipped
      };
    }
  }
};
</script>

<style scoped>
.card {
  width: 240px; /* ширина карты */
  height: 360px; /* высота карты */
  position: relative;
  perspective: 800px;
  margin-bottom: 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-family: "Gill Sans", sans-serif;
}

.card-inner {
  width: 100%;
  height: 100%;
  position: absolute;
  transform-style: preserve-3d;
  transition: transform 0.6s;
  border: 3px solid #ccc;
  border-radius: 4px;
}

.card:hover .card-inner {
  transform: rotateY(180deg);
}

.card-front,
.card-back {
  width: 100%;
  height: 100%;
  position: absolute;
  backface-visibility: hidden;
}

.card-front {
  transform: rotateY(0deg);
  background-color: #CD6600;
}

.card-back {
  transform: rotateY(180deg);
  background-color: #f5f5f5;
}

.image-wrapper {
  width: 100%;
  height: 100%;
  position: relative;
}

.image-wrapper img {
  max-width: 100%;
  max-height: 100%;
  display: block;
}

.content {
  width: 100%;
  height: 100%;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px;
  box-sizing: border-box;
  text-align: center;
}

h2 {
  text-align: center;
  font-size: 1.1rem;
  margin-bottom: 10px;
}

p.overview {
  font-size: 0.8rem;
  color: #555;
  margin-bottom: 15px;
  max-height: 120px;
  overflow: hidden;
  text-overflow: ellipsis;
}

a.movie-link {
  color: #007bff;
  text-decoration: none;
  transition: all 0.3s ease-in-out;
  border: 2px solid #ccc;
}

a.movie-link:hover {
  color: #0056b3;
  border: 2px solid #ccc;
}

.flipped {
  transform: rotateY(180deg) scale(1.2);
}

@media (max-width: 576px) {
  .card {
    width: 100%;
    height: auto;
  }
}
</style>




