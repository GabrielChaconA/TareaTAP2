<template>
  <div>
    <h1>Netflix 2</h1>
    <input type="text" v-model="searchQuery" @keypress.enter="searchMovies" placeholder="Buscar película..." id="searchInput" />
    <button @click="searchMovies" id="searchButton">Buscar</button>

    <div id="movieContainer">
      <div
        v-for="movie in movies"
        :key="movie.imdbID"
        class="movieCard"
        @click="getMovieDetails(movie.imdbID)"
      >
        <img :src="movie.Poster !== 'N/A' ? movie.Poster : 'https://via.placeholder.com/180x270?text=Sin+imagen'" :alt="movie.Title" />
        <h3>{{ movie.Title }} ({{ movie.Year }})</h3>
      </div>

      <div v-if="movies.length === 0" class="noResults">
        No se encontraron resultados para "<strong>{{ searchQuery }}</strong>".
      </div>
    </div>

    <!-- Modal -->
    <div id="modal" v-if="showModal" @click.self="closeModal">
      <div id="modalContent">
        <span id="closeModal" @click="closeModal">&times;</span>
        <div id="movieDetails" v-if="selectedMovie">
          <img :src="selectedMovie.Poster !== 'N/A' ? selectedMovie.Poster : 'https://via.placeholder.com/180x270?text=Sin+imagen'" :alt="selectedMovie.Title" />
          <h2>{{ selectedMovie.Title }} ({{ selectedMovie.Year }})</h2>
          <p><strong>Género:</strong> {{ selectedMovie.Genre }}</p>
          <p><strong>Director:</strong> {{ selectedMovie.Director }}</p>
          <p><strong>Actores:</strong> {{ selectedMovie.Actors }}</p>
          <p><strong>Duración:</strong> {{ selectedMovie.Runtime }}</p>
          <p><strong>Calificación IMDb:</strong> {{ selectedMovie.imdbRating }}</p>
          <p><strong>Sinopsis:</strong> {{ selectedMovie.Plot }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      apiKey: "thewdb",
      searchQuery: "",
      movies: [],
      selectedMovie: null,
      showModal: false
    };
  },
  methods: {
    searchMovies() {
      fetch(`https://www.omdbapi.com/?apikey=${this.apiKey}&s=${encodeURIComponent(this.searchQuery)}`)
        .then(res => res.json())
        .then(data => {
          if (data.Response === "True") {
            this.movies = data.Search;
          } else {
            this.movies = [];
          }
        })
        .catch(() => {
          this.movies = [];
        });
    },
    getMovieDetails(id) {
      fetch(`https://www.omdbapi.com/?apikey=${this.apiKey}&i=${id}&plot=full`)
        .then(res => res.json())
        .then(data => {
          if (data.Response === "True") {
            this.selectedMovie = data;
            this.showModal = true;
          }
        });
    },
    closeModal() {
      this.showModal = false;
      this.selectedMovie = null;
    }
  },
  mounted() {
    this.searchQuery = "List";
    this.searchMovies();
  }
};
</script>

<style scoped>
body {
  background-color: #0f0f0f;
  color: white;
  font-family: Arial, sans-serif;
  text-align: center;
  padding: 2rem;
}
h1 {
  color: #ff0000;
}
#searchInput {
  padding: 0.5rem;
  width: 300px;
  font-size: 1rem;
  border-radius: 4px;
}
#searchButton {
  padding: 0.5rem 1rem;
  background-color: #ff0000;
  color: black;
  font-weight: bold;
  border: none;
  cursor: pointer;
  border-radius: 4px;
}
#movieContainer {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1rem;
  margin-top: 2rem;
}
.movieCard {
  background-color: #1c1c1c;
  padding: 1rem;
  border-radius: 8px;
  width: 180px;
  box-shadow: 0 0 8px rgb(255, 255, 255);
  cursor: pointer;
  transition: transform 0.2s;
}
.movieCard:hover {
  transform: scale(1.05);
}
.movieCard img {
  width: 100%;
  border-radius: 4px;
}
.movieCard h3 {
  font-size: 1rem;
  margin: 0.5rem 0 0;
}
.noResults {
  margin-top: 2rem;
  font-size: 1.2rem;
  color: #ff6666;
}
#modal {
  display: flex;
  position: fixed;
  z-index: 10;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8);
  justify-content: center;
  align-items: center;
}
#modalContent {
  background-color: #222;
  border-radius: 8px;
  max-width: 600px;
  padding: 20px;
  color: white;
  text-align: left;
  position: relative;
}
#modalContent img {
  float: left;
  margin-right: 20px;
  width: 180px;
  border-radius: 6px;
}
#modalContent h2 {
  margin-top: 0;
  color: #ff0000;
}
#modalContent p {
  margin: 0.3rem 0;
}
#closeModal {
  position: absolute;
  top: 10px;
  right: 15px;
  font-size: 25px;
  font-weight: bold;
  color: #ff0000;
  cursor: pointer;
}
@media (max-width: 640px) {
  #modalContent {
    max-width: 90%;
  }
  #modalContent img {
    float: none;
    display: block;
    margin: 0 auto 20px;
    width: 100%;
    max-width: 300px;
  }
}
</style>
