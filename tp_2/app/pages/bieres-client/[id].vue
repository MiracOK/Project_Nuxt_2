<script setup>
const route = useRoute()
console.log(route.params.id)
const { data: posts } = await useFetch(`https://api.sampleapis.com/beers/ale/${route.params.id}`)
</script>

<template>
 <div v-if="posts" style="font-family: cursive;">
        <span>Price : {{ posts.price }}</span><br>
        <span>Name : {{ posts.name }}</span><br>
        <span>Average Ratings : {{ posts.rating.average }}</span><br>
        <span>Number of reviews : {{ posts.rating.reviews }}</span><br>
        <nuxt-img :src="posts.image" alt="Bière" class="float-left margin-fleche" style="width: 20%;" quality="30" /><br>
        <br>
    </div>
    <div v-else>
        Chargement des détails...
    </div>
</template>