<script setup>
const { data: posts } = await useFetch('https://api.sampleapis.com/beers/ale')
console.log(posts)
</script>

<template>
<div v-if="$route.params.id">
        <NuxtPage />
    </div>

    <div v-else>
        <h2>Liste de toutes les bières :</h2>

        <div v-for="post in posts" :key="post.id" style="font-family:cursive;">
            <span>Price : {{ post.price }}</span><br>
            <span>Name : {{ post.name }}</span><br>
            <span>Average Ratings : {{ post.rating.average }}</span><br>
            <span>Number of Reviews : {{ post.rating.reviews }}</span><br>
            <NuxtLink :to="`/bieres-client/${post.id}`" style="color: violet;">Voir le détail</NuxtLink>
            <br><br>
        </div>
    </div>
</template>
