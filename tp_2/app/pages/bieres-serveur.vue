<script setup>
import { ref, onMounted } from 'vue'
const posts = ref([])
const getPosts = async () => {
    posts.value = await $fetch('https://api.sampleapis.com/beers/ale')
}
onMounted(getPosts)

</script>

<template>
    <div v-for="post in posts" style="font-family:cursive;">
        <span>Price : {{ post.price }}</span><br>
        <span>Name : {{ post.name }}</span><br>
        <span>Average Ratings : {{ post.rating.average }}</span><br>
        <span>Number of Reviews : {{ post.rating.reviews }}</span><br>
        <br>
    </div>
</template>