<script setup>
import { ref, computed, onMounted } from 'vue';

const favoriteCharacters = ref([])
const query = ref('');
const searchResult = ref([]);


const charactersAsc = computed(() => {
  return favoriteCharacters.value.sort((a, b) => {
    return a.name.localeCompare(b.name)
  })
});

const searchCharacter = async () => {
  try {
    const res = await fetch(`https://rickandmortyapi.com/api/character/?name=${query.value}`)
    const data = await res.json();
    searchResult.value = data.results;
  } catch (error) {
    console.error(error);
  }
};

const handleInput = (e) => {
  if (!e.target.value) {
    searchResult.value = [];
  }

}

const AddCharacter = character => {
  searchResult.value = [];
  query.value = '';

  favoriteCharacters.value.push({
    id: character.id,
    name: character.name,
    status: character.status,
    species: character.species,
    gender: character.gender,
    origin: character.origin.name,
    image: character.image
  })

  localStorage.setItem("character", JSON.stringify(favoriteCharacters.value))
}



onMounted(() => {
  favoriteCharacters.value = JSON.parse(localStorage.getItem('character') || []);

})

const RemoveCharacter = (id) => {
  favoriteCharacters.value = favoriteCharacters.value.filter(character => character.id !== id);
  localStorage.setItem("character", JSON.stringify(favoriteCharacters.value))
}


</script>


<template>

  <main>
    <h1>Rick and Morty Characters</h1>

    <form @submit.prevent="searchCharacter">
      <input type="text" placeholder="Search a character" v-model='query' @input='handleInput' />

      <button :disabled="query.length === 0 " type="submit" class="button">Search</button>
    </form>

    <div class="results" v-if="searchResult.length > 0">

      <div class="result" v-for="character in searchResult">
        <img :src="character.image" />
        <div class="details">
          <h3>{{character.name}}</h3>
          <p>{{character.status}}</p>
          <p>{{character.species}}</p>
          <p>{{character.gender}}</p>
          <p>{{character.origin.name}}</p>
          <span class="flex-1">
            <button @click="AddCharacter(character)" class="button">Add to Favorite</button>
          </span>
        </div>
      </div>

    </div>

    <div v-show="query.length > 0 && searchResult.length === 0">
      <h2>No se ha encontro nada</h2>
    </div>


    <div class="mycharacter" v-if="favoriteCharacters.length > 0">
      <h2>My favorites characters</h2>

      <div v-for="character in charactersAsc" class="character">
        <img :src="character.image" />
        <h3>{{character.name}}</h3>
        <p>{{character.status}}</p>
        <p>{{character.species}}</p>
        <p>{{character.gender}}</p>
        <p>{{character.origin.name}}</p>
        <div class="flex-1"></div>
        <button @click="RemoveCharacter(character.id)" class="button">Remove From Favorite</button>
      </div>
    </div>



  </main>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Fira Sans', sans-serif;
}

body {
  background-color: #EEE;
}

main {
  margin: 0 auto;
  max-width: 768px;
  padding: 1.5rem;
}

h1 {
  text-align: center;
  margin-bottom: 1.5rem;
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
  background-image: linear-gradient(to right, deeppink 50%, darkviolet 50%);
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
  background-color: #fff;
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

.mycharacter h2 {
  color: #888;
  font-weight: 400;
  margin-bottom: 1.5rem;
}

.character {
  border: #888 1px solid;
  margin-bottom: 20px;
  padding: 5px;
}

.character img {
  width: 100px;
  height: 72px;
  object-fit: cover;
  border-radius: 1rem;
  margin-right: 1rem;
}

.character h3 {
  color: #888;
  font-size: 1.125rem;
}

.character p {
  color: #888;
  font-size: 1rem;
  padding: 0.4rem;
}

.character .episodes {
  margin-right: 1rem;
  color: #888;
}

.character .button:first-of-type {
  margin-right: 0.5rem;
}

.character .button:last-of-type {
  margin-right: 0;
}
</style>
