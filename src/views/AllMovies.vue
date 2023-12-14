<template>
    <div>
      <h2>Peliculas</h2>
      <div class="container">
        <div v-for="item in movies" :key="item.id" class="card">
          <img :src="item.imagen" class="card-img-top" alt="Poster de la película">
          <div class="card-body">
            <h5 class="card-title">{{ item.title }}</h5>
            <p class="card-text">{{ item.description }}</p>
            <button class="btn btn-primary mt-2" @click="verDetalle(item)">Ver Detalle</button>
            <button v-if="viewMode === 'allMovies'" class="btn btn-pink mt-2" @click="agregarFavorito(item)">
            Agregar a favoritos
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      movies: {
        type: Array,
        default: () => [],
      },
      viewMode: String, 
    },
    methods: {
      agregarFavorito(item) {
        this.$emit('add-favorite', item);
      },
      verDetalle(item) {
        this.$emit('show-detail', item);
      },
    },
  };
  </script>
  
  <style scoped>

  .container{
    display: flex;
    justify-content: space-between;
    flex-direction: row;
    flex-wrap: wrap;
  }
.card {
    background-color: rgb(47, 46, 46);
    margin-bottom: 20px;
    display: flex;
    margin-right: 2px;
    height: auto;
    width: 24%;
    text-align: center;
    align-items: center;
}

h2{
  color: #4900f3;
  font-family:'Times New Roman', Times, serif;
}

h5{
    color: white;
}

img{
    width: 100%;
}

.card-title {
  font-size: 1.2rem;
}

.card-text {
  color: #ccc;
}

button:hover{
  background-color: #ff2d55;
  border-color: #ff2d55;
}

.btn-primary {
  background-color: #e50914;
  border-color: #e50914;
  margin-right: 10px;
}

.btn-pink {
  background-color: rgb(3, 40, 139);
  border-color: rgb(101, 101, 98);
  color: white;
}
.btn-pink:hover{
  background-color: #dbb300;
  border-color: #323232;
  color: white;
}
.card-text {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
}

.card-text a {
  color: #007bff;
  text-decoration: none;
}

.card-text a:hover {
  color: #0056b3;
}
</style>