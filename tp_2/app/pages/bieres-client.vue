<script setup>
import { computed } from 'vue'

const route = useRoute()
const router = useRouter()

const { data: posts } = await useFetch('https://api.sampleapis.com/beers/ale')

const maxPrice = computed({
    get: () => route.query.pricemax || '',
    set: (val) => {
        router.push({ query: { ...route.query, pricemax: val || undefined } })
    }
})

const filteredPosts = computed(() => {
    if (!posts.value) return []
    
    const max = parseFloat(route.query.pricemax)
    
    if (max && !isNaN(max)) {
        return posts.value.filter(post => {
            const price = parseFloat(String(post.price).replace(/[^0-9.]/g, ''))
            return price <= max
        })
    }
    
    return posts.value
})
</script>

<template>
<div v-if="$route.params.id">
        <NuxtPage />
    </div>

    <div v-else>
        <h2>Liste de toutes les bières :</h2>
        <span>Filtrer par le Prix Maximum :</span>
        <input type="number" v-model="maxPrice" placeholder="Ex: 15">
        
        <div v-for="post in filteredPosts" :key="post.id" style="font-family:cursive;">
            <span>Price : {{ post.price }}</span><br>
            <span>Name : {{ post.name }}</span><br>
            <span>Average Ratings : {{ post.rating.average }}</span><br>
            <span>Number of Reviews : {{ post.rating.reviews }}</span><br>
            <NuxtLink :to="`/bieres-client/${post.id}`" style="color: violet;">Voir le détail</NuxtLink>
            <br><br>
        </div>
    </div>
</template>
