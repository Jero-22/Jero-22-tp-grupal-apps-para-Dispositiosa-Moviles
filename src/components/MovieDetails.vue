<template>
    <div>
      <h2 class="text-white">{{ selectedMovie.title }}</h2>
      <div>
        <img :src="require('./' + selectedMovie.imagen)" class="card-img-top" alt="Poster de la película">
        <div class="card-body">
          <p class="card-text">{{ selectedMovie.description }}</p>
          <p class="card-text"><strong>Género:</strong> {{ selectedMovie.genre }}</p>
          <button class="btn btn-primary mt-2" @click="hideDetail">Volver</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      selectedMovie: {
        type: Object,
        required: true
      },
      hideDetail: {
        type: Function,
        required: true
      }
    }
  };
  </script>
  <style scoped>
body{
    background-color: #640c0c;
    text-align: center;
  }
  
</style>