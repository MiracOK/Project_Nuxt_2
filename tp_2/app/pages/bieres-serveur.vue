<script setup>
import { computed, ref, onMounted } from 'vue'

const route = useRoute()
const router = useRouter()

const beerType = computed(() => route.query.type || 'ale')
const currentPage = computed(() => parseInt(route.query.page) || 1)
const itemsPerPage = 5

const maxPrice = computed({
    get: () => route.query.pricemax || '',
    set: (val) => {
        router.push({ query: { ...route.query, pricemax: val || undefined, page: 1 } })
    }
})

const { data: serverData } = await useAsyncData(
    'beers-server',
    async () => {
        return await $fetch(`https://api.sampleapis.com/beers/${beerType.value}`)
    },
    {
        watch: [beerType, () => route.query.pricemax, currentPage],
        transform: (beers) => {
            let filtered = beers
            
            const max = parseFloat(route.query.pricemax)
            if (max && !isNaN(max)) {
                filtered = filtered.filter(post => {
                    const price = parseFloat(String(post.price).replace(/[^0-9.]/g, ''))
                    return price <= max
                })
            }
            
            const total = filtered.length
            const start = (currentPage.value - 1) * itemsPerPage
            const paginated = filtered.slice(start, start + itemsPerPage)
            
            return {
                posts: paginated,
                totalPages: Math.ceil(total / itemsPerPage) || 1
            }
        }
    }
)

const changePage = (newPage) => {
    if(newPage >= 1 && newPage <= serverData.value?.totalPages) {
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

        <div v-if="serverData">
            <div v-for="post in serverData.posts" :key="post.id" style="font-family:cursive; padding: 10px; border-bottom: 1px solid #ddd;">
                <button @click="toggleFavorite(post)" style="float:right;">
                    {{ isFavorite(post.id) ? '⭐ Retirer Favori' : '☆ Ajouter Favori' }}
                </button>
                <span>Price : {{ post.price }}</span><br>
                <span>Name : {{ post.name }}</span><br>
                <span>Average Ratings : {{ post.rating.average }}</span><br>
                <span>Number of Reviews : {{ post.rating.reviews }}</span><br>
                <NuxtLink :to="{ path: `/bieres-serveur/${post.id}`, query: { type: route.query.type || 'ale' } }" style="color: violet;">Voir le détail</NuxtLink>
            </div>

            <div style="margin-top: 20px;">
                <button :disabled="currentPage === 1" @click="changePage(currentPage - 1)">Précédent</button>
                <span style="margin: 0 15px;">Page {{ currentPage }} sur {{ serverData.totalPages }}</span>
                <button :disabled="currentPage === serverData.totalPages" @click="changePage(currentPage + 1)">Suivant</button>
            </div>
        </div>
    </div>
</template>