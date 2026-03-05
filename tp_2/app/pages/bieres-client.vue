<script setup>
const { data: posts } = await useFetch('https://api.sampleapis.com/beers/ale')
console.log(posts)
</script>

<template>
    <div v-for="post in posts" style="font-family: fantasy;">
        <span>Price : {{ post.price }}</span><br>
        <span>Name : {{ post.name }}</span><br>
        <span>Average Ratings : {{ post.rating.average }}</span><br>
        <span>Number of Reviews : {{ post.rating.reviews }}</span><br>
        <br>
    </div>
</template>
