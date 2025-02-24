<template>
    <div @click="navMenu = false"
        class="text-center dark:bg-black dark:text-white h-64 flex justify-center align-middle flex-col px-2">
        <div>
            <h1 class=" text-3xl">All notes</h1>
            <p class=" font-light">{{ notes.length }} {{ notes.length === 1 ? 'note' : 'notes' }}</p>
        </div>
    </div>

    <nav class="flex justify-between px-2 sticky top-0 bg-white dark:bg-black dark:text-white py-3">
        <ul>
            <li>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h16" />
                </svg>
            </li>
        </ul>

        <ul class="flex">
            <li class="mx-3">
                <RouterLink to="/search?">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                        stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                    </svg>
                </RouterLink>
            </li>
            <li>
                <div class="relative">
                    <div v-show="navMenu" class="absolute bg-gray-200 dark:bg-gray-700 right-0 p-3 rounded-xl w-28">
                        <ul>
                            <li>Edit</li>
                            <li>View</li>
                        </ul>
                    </div>
                    <svg @click="navMenu = true" xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none"
                        viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M12 5v.01M12 12v.01M12 19v.01M12 6a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2z" />
                    </svg>
                </div>
            </li>
        </ul>
    </nav>

    <div @click="navMenu = false" v-if="notes.length === 0"
        class="text-center h-[95vh] flex flex-col px-2 dark:bg-black dark:text-white">
        <div>
            <h1 class=" text-2xl font-semibold">You have no notes</h1>
            <p class=" text-lg font-thin">Click the button on the bottom right to create the first one</p>
        </div>
    </div>

    <section v-else class="grid grid-cols-2 px-2 gap-1 dark:bg-black pt-2 dark:text-white">
        <RouterLink :to="`/notes/${note.id}`" v-for="(note, i) in notes" :key="i" @click.prevent="showNote(note)">
            <div class="border-2 h-52 rounded-2xl dark:bg-gray-900 dark:text-white px-2 py-2 overflow-y-scroll">
                <div class="prose prose-green dark:prose-invert" v-html="marked.parse(note?.body || '')"></div>
            </div>
            <div class="text-center">
                <h1 class=" font-semibold truncate">{{ note?.title }}</h1>
                <p class=" font-thin text-sm">
                    {{ new Date(note?.createdAt).toLocaleDateString() }}
                    {{ new Date(note?.createdAt).toLocaleTimeString() }}</p>
            </div>
        </RouterLink>
    </section>


    <!--Floating button for adding a new note-->
    <div class=" fixed bottom-1 right-1">
        <RouterLink to="/create">
            <button class="bg-white dark:bg-black dark:text-white p-4 rounded-full shadow-lg">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round"
                        d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                </svg>
            </button>
        </RouterLink>
    </div>
    <!--/Floating button for adding a new note-->


</template>

<script setup>
import { marked } from 'marked'
import { reactive, ref } from '@vue/reactivity';
import { onMounted } from '@vue/runtime-core';
import { db } from '../db';

const showCreateForm = ref(false)
const navMenu = ref(false)
const notes = ref([])
const viewNoteComponent = ref(false)
const currentActiveNote = ref('')
const newNoteData = reactive({
    title: null,
    body: null
})
const currentNote = reactive({
    id: null,
    title: null,
    body: null
})

const getNotes = async () => {
    const nts = await db.notes.reverse().toArray()
    notes.value = nts
    return
}
const showNote = (note) => {
    currentActiveNote.value = note
    viewNoteComponent.value = true
}
onMounted(() => {
    getNotes()
})
</script>
