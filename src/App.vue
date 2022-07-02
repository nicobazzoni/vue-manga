<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import SVGheart from './heart.svg';
const query = ref('')
const my_manga = ref([])
const search_results = ref([])
const manga = ref([])

const my_manga_asc = computed(() => {
	return my_manga.value.sort((a, b) => {
		return a.title.localeCompare(b.title)
	})
})



const searchManga = () => {
	const url = `https://api.jikan.moe/v4/manga?q=${query.value}`
	fetch(url)
		.then(res => res.json())
		.then(data => {
			search_results.value = data.data
		})
}



const handleInput = (e) => {
	if (!e.target.value) {
		search_results.value = []
	}
}



const addManga = (manga) => {
	search_results.value = []
	query.value = ''

	my_manga.value.push({
		id: manga.mal_id,
		title: manga.title,
		image: manga.images.jpg.image_url,
		total_episodes: manga.episodes,
		watched_episodes: 0,
	})

	localStorage.setItem('my_manga', JSON.stringify(my_manga.value))
}

const upRating = (manga) => {
	manga.watched_episodes++
	localStorage.setItem('my_manga', JSON.stringify(my_manga.value))
}

const downRating = (manga) => {
	manga.watched_episodes--
	localStorage.setItem('my_manga', JSON.stringify(my_manga.value))
}

const toggleFavorite = (manga) => {
      this.favorite = !this.favorite;
	  localStorage.setItem('my_manga', JSON.stringify(my_manga.value))
}


onMounted(() => {
	my_manga.value = JSON.parse(localStorage.getItem('my_manga')) || []
})


    const  togglefav = function(){
        this.$emit('togglefav',!this.is_fav);
	  }
  
</script>

<template>
	<main>
		<h1> Manga </h1>

		<form @submit.prevent="searchManga">
			<input type="text" placeholder="Search for an manga..." v-model="query" @input="handleInput" />
			<button type="submit" class="button">Search</button>
		</form>

		<div class="results" v-if="search_results.length > 0">
			<div v-for="manga in search_results" class="result" :key="manga" >
				<img :src="manga.images.jpg.image_url" />
				<div class="details">
					<h3>{{ manga.title }}</h3>
					<p :title="manga.synopsis" v-if="manga.synopsis">{{ manga.synopsis.slice(0, 120) }}...</p>
					<span class="flex-1"></span>
					<button @click="addManga(manga)" class="button">Add to Manga List</button>
				</div>
			</div>
		</div>

		<div class="mymanga" v-if="my_manga.length > 0">
			<h2>My Manga</h2>

			<div v-for="manga in my_manga_asc" class="manga" :key="manga" >
				<img :src="manga.image" />
				<h3>{{ manga.title }}</h3>
				<div class="flex-1"></div>
				<span class="episodes">{{ manga.watched_episodes }} / {{ manga.total_episodes }}</span>
				<button 
					v-if="manga.total_episodes !== manga.watched_episodes" 
					@click="upRating(manga)" class="button">+</button>
				<button 
					v-if="manga.watched_episodes > 0"
					@click="downRating(manga)" class="button">-</button>
                   
					
				</div>
                   
     
				</div>
			</main>
		</template>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Helvetica Neue',Helvetica;
	
}

body {
	background-color: rgb(43, 41, 41);
}



main {
	margin: 0 auto;
	max-width: 768px;
	padding: 1.5rem;
}

h1 {
	
	text-align: center;
	margin-bottom: 1.5rem;
  color: rgb(236, 231, 238);
  background: none;
  font-family: 'Permanent Marker', cursive;
  
  border: 1px solid;
  border-end-end-radius: 3rem;
  border-color: rgb(249, 0, 253);
  background-color: rgb(31, 29, 29);
  border: 2px;;
  font-size: 3.25rem;
  
}

h1:hover {
	
color: rgb(249, 0, 253)
}

form {
	display: flex;
	max-width: 480px;
	margin: 0 auto 1.5rem;
}

form input {
	appearance: none;
	outline: none;
	border: none;
	background: white;

	display: block;
	color: #888;
	font-size: 1.125rem;
	padding: 0.5rem 1rem;
	width: 100%;
}


.button {
	appearance: none;
	outline: none;
	border: none;
	background: none;
	cursor: pointer;

	display: block;
	padding: 0.5rem 1rem;
	background-image: linear-gradient(to right, deeppink 50%, rgb(0, 198, 253) 50%);
	background-size: 200%;
	color: white;
	font-size: 1.125rem;
	font-weight: bold;
	text-transform: uppercase;
	transition: 0.4s;
}

.button:hover {
	background-position: right;
}

.results {
	background-color: rgb(254, 253, 253);
	border-radius: 0.5rem;
	box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	max-height: 480px;
	overflow-y: scroll;
	margin-bottom: 1.5rem;
}

.result {
	display: flex;
	margin: 1rem;
	padding: 1rem;
	border: 1px solid #ccc;
	border-radius: 5px;
	transition: 0.4s;
}

.result img {
	width: 100px;
	border-radius: 1rem;
	margin-right: 1rem;
}

.details {
	flex: 1 1 0%;
	display: flex;
	align-items: flex-start;
	flex-direction: column;
}

.details h3 {
	font-size: 1.25rem;
	margin-bottom: 0.5rem;
}

.details p {
	font-size: 0.875rem;
	margin-bottom: 1rem;
}

.details .button {
	margin-left: auto;
}

.flex-1 {
	display: block;
	flex: 1 1 0%;
}

.mymanga h2 {
	color: rgb(3, 213, 255);
	font-weight: 400;
	margin-bottom: 1.5rem;
	 font-family: 'Permanent Marker', cursive;
}

.mymanga h2:hover {
	color: rgb(249, 0, 253)
}

.mymanga .manga {
	display: flex;
	align-items: center;
	margin-bottom: 1.5rem;
	background-color: #FFF;
	padding: 1rem;
	border-radius: 0.5rem;
	box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
}

.manga img {
	width: 72px;
	height: 72px;
	object-fit: cover;
	border-radius: 1rem;
	margin-right: 1rem;
}

.manga h3 {
	color: #888;
	font-size: 1.125rem;
}

.manga .episodes {
	margin-right: 1rem;
	color: #888;
}

.manga .button:first-of-type {
	margin-right: 0.5rem;
}

.manga .button:last-of-type {
	margin-right: 0;
}
</style>
