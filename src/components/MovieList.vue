<template>
  <div class="container">
    <h2>🎬 Películas</h2>
    <form @submit.prevent="addMovie" class="form">
      <input v-model="title" placeholder="Título" required />
      <input v-model="description" placeholder="Descripción" required />
      <input v-model="url" placeholder="Enlace del video" required />
      <button type="submit">Agregar</button>
    </form>

    <div class="movie-card" v-for="movie in movies" :key="movie.id">
      <h3>{{ movie.title }}</h3>
      <p>{{ movie.description }}</p>
      <iframe
        v-if="movie.url"
        :src="movie.url"
        width="100%"
        height="200"
        frameborder="0"
        allowfullscreen
      ></iframe>
      <button class="delete-btn" @click="deleteMovie(movie.id)">Eliminar</button>
    </div>
  </div>
</template>

<script>
import { db, collection, addDoc, deleteDoc, doc, onSnapshot } from '../firebase';

export default {
  data() {
    return {
      title: '',
      description: '',
      url: '',
      movies: []
    };
  },
  created() {
    const moviesCollection = collection(db, 'movies');
    onSnapshot(moviesCollection, snapshot => {
      this.movies = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
    });
  },
  methods: {
    async addMovie() {
      const moviesCollection = collection(db, 'movies');
      await addDoc(moviesCollection, {
        title: this.title,
        description: this.description,
        url: this.url
      });
      this.title = this.description = this.url = '';
    },
    async deleteMovie(id) {
      const movieDoc = doc(db, 'movies', id);
      await deleteDoc(movieDoc);
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 40px auto;
  padding: 20px;
  background: #f9fafb;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  font-family: 'Segoe UI', sans-serif;
}

h2 {
  text-align: center;
  color: #333;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-bottom: 24px;
}

.form input {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
}

.form button {
  padding: 10px;
  background: #4f46e5;
  color: white;
  font-weight: bold;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s ease;
}

.form button:hover {
  background: #3730a3;
}

.movie-card {
  background: white;
  border: 1px solid #eee;
  border-radius: 12px;
  padding: 16px;
  margin-bottom: 16px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.05);
}

.movie-card h3 {
  margin: 0 0 8px;
  color: #111827;
}

.movie-card p {
  margin: 0 0 12px;
  color: #374151;
}

.delete-btn {
  padding: 8px 12px;
  background: #dc2626;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
}

.delete-btn:hover {
  background: #b91c1c;
}
</style>
