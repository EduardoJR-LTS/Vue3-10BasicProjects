<template>
    <div :class="{ 'dark' : isDarkTheme }">

        <div class="container">
            <div class="header-container">
                <h1>Buscar en wikipedia</h1>

                <button id="theme-toggler" @click="toggleTheme" class="theme-toggler">
                    {{ isDarkTheme ? 'Light' : 'Dark' }}
                </button>
            </div>
        </div>
    

        <form @submit.prevent="submitSearch">
            <input type="text" v-model="searchQuery" placeholder="Ingresa algún termino" />
            <button type="submit">Buscar</button>
        </form>

        <div id="search-result">
            <div v-if="isLoading" class="spiner">Loading...</div>
            <div v-if="searchResults.length">
                <ul>
                    <li v-for="(result) in searchResults" :key="result.pageid" class="item">
                        <h3>
                            <a :href="`https://es.wikipedia.org/?curid=${result.pageid}`" target="_blank">
                                {{ result.title }}
                            </a>
                        </h3>
                        <a :href="`https://es.wikipedia.org/?curid=${result.pageid}`">
                            {{ `https://es.wikipedia.org/?curid=${result.pageid}` }}
                        </a>
                        <p>
                            {{ stripTags(result.snippet) }}
                        </p>
                        <hr>
                    </li>
                </ul>
            </div>
        </div>

    </div>
</template>

<script setup>
import { ref } from 'vue';

const searchQuery = ref('')
const searchResults = ref([])
const isLoading = ref(false)
const error = ref(null)
const isDarkTheme = ref(false)

// https://es.wikipedia.org/w/api.php
const searchWikipedia = async (query) => {
    const encodedQuery = encodeURIComponent(query)    
    const endPoint = `https://es.wikipedia.org/w/api.php?action=query&list=search&srprop=snippet&format=json&origin=*&utf8=&srsearch=${encodedQuery}`
    // https//en.wikipedia.org/w/api.php?action=query&list=search&prop=info&inprop=url&utf8=&format=json&origin=*&srlimit10&srsearch
    try {
        isLoading.value = true
        const response = await fetch(endPoint)
        console.log(response, 'res');
        
        const data = await response.json()
        console.log(data, 'data');

        if (data.query && data.query.search) {
            searchResults.value = data.query.search
            error.value = null
        } else {
            searchResults.value = []
            error.value = 'No se encontraron resultados'
        }

    } catch (error) {
        console.log('Error fetching data', error);
        searchResults.value = []
        error.value = 'Ocurrió un error mientrass se solicitaban los datos'        
    } finally {
        isLoading.value = false
    }

}

const toggleTheme = () => {
    isDarkTheme.value = !isDarkTheme.value
}

const submitSearch = () => {
    if ( searchQuery.value.trim !== '') {        
        searchWikipedia(searchQuery.value)
    }
}

const stripTags = (str) => {
    let doc = new DOMParser().parseFromString(str, 'text/html');
    return doc.body.textContent || "";
}


</script>

<style scoped>
body {
  padding: 25px;
  background-color: white;
  color: black;
  font-size: 25px;
}

.dark {
  background-color: black;
  color: white;
}

.theme-toggler {
    float: right;
}

button {
    width: 50px;
    height: 20px;
    text-align: center;
    padding: 0px;
    box-sizing : content-box;
}

input {
    width: 300px;
    height: 24px;
    margin-right: 10px;
}
</style>
