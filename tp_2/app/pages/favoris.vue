<template>
  <div style="padding: 20px;">
    <h1 style="color: #d11141;">Mes Bières Favorites</h1>

    <div v-if="favorites.length === 0" style="margin-top: 20px;">
      Aucune bière en favori pour le moment.
    </div>

    <div v-else>
      <div v-for="post in favorites" :key="post.id" style="font-family:cursive; padding: 15px; border: 1px solid #ddd; border-radius: 8px; margin-bottom: 20px;">
        <button @click="removeFavorite(post.id)" style="float: right; color: red;">Retirer</button>
        <span>Price : {{ post.price }}</span><br>
        <span>Name : {{ post.name }}</span><br>
        <span>Average Ratings : {{ post.rating?.average }}</span><br>
        <span>Number of Reviews : {{ post.rating?.reviews }}</span><br>
        <NuxtLink :to="`/bieres-client/${post.id}`" style="color: violet;">Voir le détail</NuxtLink>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const favorites = ref([])

onMounted(() => {
  favorites.value = JSON.parse(localStorage.getItem('favoris') || '[]')
})

const removeFavorite = (id) => {
  favorites.value = favorites.value.filter(b => b.id !== id)
  localStorage.setItem('favoris', JSON.stringify(favorites.value))
}
</script>