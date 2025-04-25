<template>
  <link href="https://fonts.cdnfonts.com/css/games" rel="stylesheet">
  <header
          class="mb-4 d-flex align-items-center"
          style="gap: 1rem; font-family: 'Games', sans-serif;">
    <h1 class="bg-black text-white text-center flex-grow-1" style="padding: 1vh;">
      Games List
    </h1>
  </header>



  <div class="container mx-auto">
    <div class="row row-cols-1 g-4">
      <div v-for="game in games" :key="game._id" class="col">
        <div class="card h-100 shadow-sm border-primary position-relative">
          <span
              class="position-absolute top-0 end-0 m-2 badge rounded-circle bg-success"
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

import { onMounted } from 'vue'
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

import { useAsyncData } from '#app';
const { data: games } = await useAsyncData('games', async () => {

  const response = await fetch('http://localhost:5000/api/games');
  if (!response.ok) {
    throw new Error('Failed to fetch games');
  }
  return await response.json();

});

const getRatingColor = (rating) => {
  if (rating >= 90) return 'bg-success'
  else if (rating >= 70) return 'bg-warning text-dark'
  else return 'bg-danger'
}

</script>

<style lang="css" scoped>

@import url('https://fonts.cdnfonts.com/css/games');

.container.my-5 {
  margin-bottom: 0 !important;
  margin-top: 0 !important;
  padding-bottom: 3rem !important;
  padding-top: 3rem !important;
}

.bg-black{
  font-size: 5rem;
}
</style>

