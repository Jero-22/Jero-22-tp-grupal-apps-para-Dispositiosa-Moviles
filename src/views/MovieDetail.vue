<template>
  <div v-if="movie" class="movie-details row">
    <h2 class="mt-3">{{ movie.title }}</h2>
    <div>
      <img :src="movie.imagen" class="card-img-top" alt="Poster de la película">
      <div class="card-body">
        <p class="card-text">{{ movie.description }}</p>
        <p class="card-text"><strong>Género:</strong> {{ getGenresList(movie.genre) }}</p>
        <div v-if="movie.trailerKey">
          <iframe width="100%" height="315" :src="'https://www.youtube.com/embed/' + movie.trailerKey" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
        </div>
        <div v-else>
          <p>Cargando tráiler...</p>
        </div>
        <button v-if="!isInFavorites" class="btn btn-primary mt-2" @click="addToFavorites">Agregar a Favoritos</button>
        <button class="btn btn-primary mt-2" @click="goBack">Volver</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    movie: {
      type: Object,
      required: true,
    },
  },
  methods: {
    goBack() {
      this.$emit('back');
    },
   getGenresList(genreIds) {
      const genreMap = {
        28: 'Acción',
        18: 'Drama',
        35: 'Comedia',
        878: 'Ciencia Ficcion',
      };


      const genreNames = genreIds.map((id) => genreMap[id] || 'Desconocido');

      return genreNames.join(', ');
    },
    addToFavorites() {
      this.$emit('add-favorite', this.movie);
    },
    removeFromFavorites() {
      this.$emit('remove-favorite', this.movie);
    },
  },
  computed: {
    isInFavorites() {
      return this.$parent.favoriteMovies.some((favMovie) => favMovie.id === this.movie.id);
    },
  },
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.movie-details {
  text-align: center;
}

img {
  width: 20%;
  max-height: 400px; 
  object-fit: cover; 
  border-radius: 8px;
  margin-bottom: 20px;
}

h2 {
  font-size: 2rem;
  color: white;
  margin-bottom: 10px;
}

p {
  color: #ccc;
  margin-bottom: 15px;
}

iframe{
  width: 50%;
}

.btn-primary {
  background-color: #e50914;
  border-color: #e50914;
  margin-right: 10px;
  margin-bottom: 20px;
}

.btn-danger {
  background-color: #ff3e3e;
  border-color: #ff3e3e;
}
</style>