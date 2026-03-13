<script setup>
import { computed, ref, onMounted } from 'vue'

const route = useRoute()
const router = useRouter()

const beerType = computed(() => route.query.type || 'ale')
const { data: posts } = await useFetch(() => `https://api.sampleapis.com/beers/${beerType.value}`)

const maxPrice = computed({
    get: () => route.query.pricemax || '',
    set: (val) => {
        router.push({ query: { ...route.query, pricemax: val || undefined, page: 1 } }) 
    }
})

const filteredPosts = computed(() => {
    if (!posts.value) return []
    let result = posts.value
    const max = parseFloat(route.query.pricemax)
    if (max && !isNaN(max)) {
        result = result.filter(post => {
            const price = parseFloat(String(post.price).replace(/[^0-9.]/g, ''))
            return price <= max
        })
    }
    return result
})

const itemsPerPage = 5
const currentPage = computed(() => parseInt(route.query.page) || 1)
const totalPages = computed(() => Math.ceil(filteredPosts.value.length / itemsPerPage))

const paginatedPosts = computed(() => {
    const start = (currentPage.value - 1) * itemsPerPage
    return filteredPosts.value.slice(start, start + itemsPerPage)
})

const changePage = (newPage) => {
    if(newPage >= 1 && newPage <= totalPages.value) {
        router.push({ query: { ...route.query, page: newPage } })
    }
}

const favorites = ref([])

onMounted(() => {
    favorites.value = JSON.parse(localStorage.getItem('favoris') || '[]')
})

const toggleFavorite = (post) => {
    const index = favorites.value.findIndex(b => b.id === post.id)
    if(index === -1) {
        favorites.value.push(post)
    } else {
        favorites.value.splice(index, 1)
    }
    localStorage.setItem('favoris', JSON.stringify(favorites.value))
}

const isFavorite = (id) => favorites.value.some(b => b.id === id)
</script>

<template>
<div v-if="$route.params.id">
        <NuxtPage />
    </div>

    <div v-else>
        <h2>Liste de toutes les bières :</h2>
        <span>Filtrer par le Prix Maximum :</span>
        <input type="number" v-model="maxPrice" placeholder="Ex: 15">
        
        <div v-for="post in paginatedPosts" :key="post.id" style="font-family:cursive; padding: 10px; border-bottom: 1px solid #ddd;">
            <button @click="toggleFavorite(post)" style="float:right;">
                {{ isFavorite(post.id) ? '⭐ Retirer Favori' : '☆ Ajouter Favori' }}
            </button>
            <span>Price : {{ post.price }}</span><br>
            <span>Name : {{ post.name }}</span><br>
            <span>Average Ratings : {{ post.rating.average }}</span><br>
            <span>Number of Reviews : {{ post.rating.reviews }}</span><br>
            <NuxtLink :to="{ path: `/bieres-client/${post.id}`, query: { type: route.query.type || 'ale' } }" style="color: violet;">Voir le détail</NuxtLink>
        </div>

        <div style="margin-top: 20px;">
            <button :disabled="currentPage === 1" @click="changePage(currentPage - 1)">Précédent</button>
            <span style="margin: 0 15px;">Page {{ currentPage }} sur {{ totalPages }}</span>
            <button :disabled="currentPage === totalPages" @click="changePage(currentPage + 1)">Suivant</button>
        </div>
    </div>
</template>
