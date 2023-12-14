<template>
  <div id="app" ref="app">
   
    <nav class="navbar navbar-dark bg-dark">
      <div class="container">
      <div class="row">
        
      <h1 id="titulo"><span>Mega</span>Pelis</h1>
        
        <div class="col-md-6">
          
          <select class="form-select" v-model="selectedGenre">
            <option value="">Todos los géneros</option>
             <option value="28">Acción</option>
            <option value="878">Ciencia Ficcion</option>
            <option value="18">Drama</option>
           <option value="35">Comedia</option>
            
          </select>
        </div>
        <div class="col-md-6">
          
          <input type="text" class="form-control" v-model="searchTitle" placeholder="Buscar por título">
        </div>
        
      </div>
      <button v-if="viewMode !== 'favoriteMovies'" class="botonFav" @click="showFavoriteMovies">Favoritos</button>
        <div class="container mt-3" v-if="viewMode === 'allMovies' && !showDetail">
    </div>
      </div>
     
      <div class="container mt-3">
        <div class="row">
          <div class="col-md-6">
            <button v-if="viewMode !== 'allMovies'" class="btn btn-primary" @click="showAllMovies">Todas las Películas</button>
            
          </div>
        </div>
      </div>
    </nav>

    

    <div class="container mt-3">
      <movie-detail v-if="showDetail" :movie="selectedMovie" @back="goBackToList" @add-favorite="agregarFavorito" @remove-favorite="removerFavorito"></movie-detail>

      <favorite-movies
        v-if="viewMode === 'favoriteMovies' && !showDetail"
        :movies="favoriteMovies"
        @detail="verDetalle"
        @remove-favorite="removerFavorito"
      ></favorite-movies>

      <popular-movies v-if="viewMode === 'allMovies' && !showDetail" :movies="popularMovies" @add-favorite="agregarFavorito" @show-detail="verDetalle"></popular-movies>

      <div class="row" v-if="viewMode === 'allMovies' && !showDetail">
        <all-movies :movies="currentItemList" :viewMode="viewMode" @add-favorite="agregarFavorito" @show-detail="verDetalle"></all-movies>
      </div>

      <div v-if="currentItemList.length === 0 && !showDetail && !favoriteMovies" class="mt-3">
        <p>No se encontraron películas que coincidan con la búsqueda.</p>
      </div>
    </div>
  </div>
</template>



<script>
import axios from 'axios';
import MovieDetail from '@/views/MovieDetail.vue'; 
import FavoriteMovies from './views/FavoriteMovies.vue';
import AllMovies from './views/AllMovies.vue';
import PopularMovies from './components/PopularMovies.vue';

export default {
  data() {
    return {
      selectedGenre: '',
      searchTitle: '',
      movies: [],
      favoriteMovies: [],
      tmdbApiKey: 'f6e1660c2c9583d667f15a17eb6d81dd',
      viewMode: 'allMovies',
      selectedMovie: {
        trailerKey: null,
      },
      showDetail: false, 
      popularMovies: [], 
    };
  },
  computed: {
    filteredMovies() {
  return this.movies.filter((movie) =>
    (this.selectedGenre === '' || movie.genre.includes(parseInt(this.selectedGenre))) &&
    movie.title.toLowerCase().includes(this.searchTitle.toLowerCase())
  );
},
currentItemList() {
  if (this.viewMode === 'allMovies') {
    return this.filteredMovies;
  } else if (this.viewMode === 'favoriteMovies') {
    return this.favoriteMovies;
  } else {
    return this.popularMovies;
  }
},
  },
  methods: {
    async fetchMoviesFromTmdb() {
      try {
        
        const popularResponse = await axios.get(
          `https://api.themoviedb.org/3/discover/movie?api_key=${this.tmdbApiKey}&language=es&sort_by=popularity.desc&vote_average.gte=8.0`
        );

        this.popularMovies = popularResponse.data.results.map((tmdbMovie) => ({
          id: tmdbMovie.id,
          title: tmdbMovie.title,
          description: tmdbMovie.overview,
          genre: tmdbMovie.genre_ids,
          imagen: `https://image.tmdb.org/t/p/w500${tmdbMovie.poster_path}`,
        }));

       
        const discoverResponse = await axios.get(
          `https://api.themoviedb.org/3/discover/movie?api_key=${this.tmdbApiKey}&language=es`
        );

        this.movies = discoverResponse.data.results.map((tmdbMovie) => ({
          id: tmdbMovie.id,
          title: tmdbMovie.title,
          description: tmdbMovie.overview,
          genre: tmdbMovie.genre_ids,
          imagen: `https://image.tmdb.org/t/p/w500${tmdbMovie.poster_path}`,
        }));

        
        this.movies = this.movies.filter(
          (movie) => !this.favoriteMovies.find((favMovie) => favMovie.id === movie.id) &&
                     !this.popularMovies.find((popMovie) => popMovie.id === movie.id)
        );

        localStorage.setItem('movies', JSON.stringify(this.movies));
        localStorage.setItem('popularMovies', JSON.stringify(this.popularMovies));
      } catch (error) {
        console.error('Error al obtener datos de TMDb:', error);
      }
    },
    async fetchMovieTrailer(movieId) {
      try {
        const response = await axios.get(
          `https://api.themoviedb.org/3/movie/${movieId}/videos?api_key=${this.tmdbApiKey}&language=es`
        );

        const trailers = response.data.results;
        if (trailers.length > 0) {
          this.selectedMovie.trailerKey = trailers[0].key;
        }
      } catch (error) {
        console.error('Error al obtener el tráiler de la película:', error);
      }
    },
    async verDetalle(item) {
      this.selectedMovie = item;
      await this.fetchMovieTrailer(item.id);
      this.showDetail = true;
    },
    goBackToList() {
      this.showDetail = false;
      this.selectedMovie = {
        trailerKey: null,
      };
    },
    agregarFavorito(item) {
      const existingMovie = this.favoriteMovies.find((movie) => movie.id === item.id);
      if (!existingMovie) {
        this.favoriteMovies.push(item);
        localStorage.setItem('favoriteMovies', JSON.stringify(this.favoriteMovies));
        console.log('Elemento agregado a favoritos. favoriteMovies:', this.favoriteMovies);
      } else {
        console.log('La película ya está en la lista de favoritos.');
      }
    },
    removerFavorito(item) {
      const index = this.favoriteMovies.findIndex((movie) => movie.id === item.id);
      if (index !== -1) {
        this.favoriteMovies.splice(index, 1);
        localStorage.setItem('favoriteMovies', JSON.stringify(this.favoriteMovies));
        console.log('Elemento removido de favoritos. favoriteMovies:', this.favoriteMovies);

        this.fetchMoviesFromTmdb();
      } else {
        console.log('La película no está en la lista de favoritos.');
      }
    },
    showAllMovies() {
      this.viewMode = 'allMovies';
      this.showDetail = false; 
    },
    showFavoriteMovies() {
      this.viewMode = 'favoriteMovies';
      this.showDetail = false; 
    },
  },
  mounted() {
    console.log('Componente montado. favoriteMovies:', this.favoriteMovies);
    this.fetchMoviesFromTmdb();
  },
  components: {
    'movie-detail': MovieDetail,
    'favorite-movies': FavoriteMovies,
    'all-movies': AllMovies,
    'popular-movies': PopularMovies,
  },
};
</script>

<style>
  #app {
    background-color: #640c0c;
    text-align: center;
  }

  .btn-pink {
    background-color: #ff69b4;
    color: white;
  }

  .container{
    text-align: center;
  }

  #titulo{
    color: rgb(255, 187, 0);
    font-size: 28px;
    text-align: center;
  }
  span {
      color:rgb(255, 187, 0);
    }
</style>
