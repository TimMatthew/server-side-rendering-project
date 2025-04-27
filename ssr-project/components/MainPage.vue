<template>
  <link href="https://fonts.cdnfonts.com/css/games" rel="stylesheet">

  <header
      class="mb-4 d-flex flex-column align-items-center w-100"
      style="gap: 1rem; font-family: 'Games', sans-serif; background-color: black;"
  >
    <h1 class="text-white text-center" style="font-size: 5rem;">
      Games List
    </h1>

    <div class="input-group mb-3"
         style="max-width: 500px; width: 100%; font-family: var(--bs-body-font-family),serif; background-color: black;">
      <input
          v-model="searchQuery"
          @keyup.enter="filterGames"
          type="text"
          class="form-control"
          placeholder="Search games..."
      />
      <button class="btn btn-primary" type="button" @click="filterGames">
        Search
      </button>
    </div>
  </header>



  <div class="container mx-auto">
    <div v-if="pending" class="text-center">
      <p>Loading...</p>
    </div>

    <div v-else class="row row-cols-1 g-4">
      <div v-for="game in games" :key="game._id" class="col">
        <div class="card h-100 shadow-sm border-primary position-relative">
          <span
              class="position-absolute top-0 end-0 m-2 badge rounded-circle"
              :class="getRatingColor(game.metacritic_rating)"
              style="width: 40px; height: 40px; display: flex; align-items: center; justify-content: center; font-size: 1rem; margin-top: 10px; margin-right: 10px"
          >
            {{ game.metacritic_rating }}
          </span>

          <div class="card-body">
            <h5 class="card-title text-primary">
              {{ game.game_title }}
              <small class="text-muted">({{ game.release_year }})</small>
            </h5>
            <p class="card-text mb-1">
              <strong>Developer:</strong> {{ game.developer }}
            </p>
            <p class="card-text mb-1">
              <strong>Publisher:</strong> {{ game.publisher }}
            </p>
            <p class="card-text mb-1">
              <strong>Genres:</strong> {{ game.genres.join(', ') }}
            </p>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { useAsyncData, useRoute, useRouter } from '#imports'
import {onMounted, ref} from 'vue'

const router = useRouter()
const route = useRoute()

const searchQuery = ref('')

onMounted(() => {
  const nuxt = document.getElementById("__nuxt")
  if (nuxt) {
    nuxt.style.minHeight = "100vh"
    nuxt.style.width = "100vw"
    nuxt.style.backgroundImage = "url('/Fabled-Sunset-Gradient-4K-Wallpaper-7132.jpg')"
    nuxt.style.backgroundSize = "cover"
    nuxt.style.backgroundPosition = "center"
    nuxt.style.backgroundRepeat = "no-repeat"
  }
})

const { data: games, pending} = await useAsyncData('games', async (ctx) => {
  const search = ctx.ssrContext?.event?.req?.url.includes('search=')
      ? new URLSearchParams(ctx.ssrContext.event.req.url.split('?')[1]).get('search')
      : (route.query.search || '')

  const url = search
      ? `http://localhost:5000/api/filter?search=${encodeURIComponent(search)}`
      : 'http://localhost:5000/api/games'

  const response = await fetch(url)
  if (!response.ok) {
    throw new Error('Failed to fetch games')
  }
  return await response.json()
}, {
  watch: [() => route.query.search],
})

if (process.client) {
  searchQuery.value = route.query.search || ''
}

const filterGames = () => {
  if (!searchQuery.value.trim()) {
    router.push({ path: router.currentRoute.value.path, query: {} })
  } else {
    router.push({ query: { search: searchQuery.value.trim() } })
  }
}

const getRatingColor = (rating) => {
  if (rating >= 90) return 'bg-success'
  else if (rating >= 70) return 'bg-warning text-dark'
  else return 'bg-danger'
}
</script>