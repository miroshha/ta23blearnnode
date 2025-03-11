<script setup>

import axios from 'axios';
import { onMounted, ref } from 'vue';
import CharacterCard from '../components/CharacterCard.vue';
import PagedPagination from '../components/PagedPagination.vue';

const characters = ref([]);
const pagination = ref({});
let currentPage = ref(1)
await page(currentPage.value)


async function getCharacters(url) {
    let response = await axios.get(url);
    characters.value.push(...response.data.results);
    pagination.value = response.data.info;
}

async function next() {
    currentPage.value++
    await getCharacters(pagination.value.next);
}

async function prev() {
    currentPage.value--
    await getCharacters(pagination.value.prev);
}

async function page(page) {
    currentPage.value = page
    getCharacters(`https://rickandmortyapi.com/api/character?page=${page}`)
}

onMounted(() => {
    document.addEventListener('scroll', () => {
        if (window.scrollY + window.innerHeight > document.body.clientHeight-300) {
            if (pagination.value.next) {
                next()
            }
        }
        // console.log(document.body.clientHeight, window.scrollY + window.innerHeight)
    })
})

</script>
<template>
    <PagedPagination :pagination="pagination" :current="currentPage" @next="next" @prev="prev" @page="page">
    </PagedPagination>
    <div class="columns is-multiline">
        <div v-for="character in characters" class="column is-one-quarter">
            <CharacterCard :character="character"></CharacterCard>
        </div>
    </div>

</template>