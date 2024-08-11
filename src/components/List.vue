<script setup lang="ts">
import { ref, onMounted } from 'vue'
import type { PropType } from 'vue';
import type { Command } from '../types'
import CopyPaste from './CopyPaste.vue'
import NewCopyPaste from './NewCopyPaste.vue';

defineOptions({ inheritAttrs: false })

const {commands} = defineProps({
    commands: {
        type: Array as PropType<Command[]>,
        required: true
    }
})
let filter = ref([...commands])
let showForm = ref(false)

const readStorage = () => {
    const storage = localStorage.getItem('commands')
    if(storage) return JSON.parse(storage)
    else return [] 
}

const saveStorage = (commands:Array<Command>) => {
    localStorage.setItem('commands', JSON.stringify(commands))
}

const saveCommand = (newCommand:Command) => {
    const storageCommands = readStorage()
    storageCommands.push(newCommand)
    saveStorage(storageCommands)
    filter.value = storageCommands
}

const handleNewCommand = (newCommand:Command) => {
    saveCommand(newCommand)
    showForm.value = false
}

const handleChange = (event) => {
    const newSearch = event.target.value
    const storageCommands = readStorage()
    filter.value = storageCommands
    if(newSearch){
        filter.value = storageCommands.filter((command) => {
            return command.title?.toUpperCase().includes(newSearch?.toUpperCase()) 
            || command.description?.toUpperCase().includes(newSearch?.toUpperCase()) 
            || command.command?.toUpperCase().includes(newSearch?.toUpperCase()) 
        })  
    }
}

onMounted(() => {
    const storageCommands = readStorage()
    if(storageCommands.length > 0) filter.value = storageCommands
    else saveStorage(commands)

    document.addEventListener('keydown', (event) => {    
        if(event.ctrlKey && event.key === 'f'){
            event.preventDefault()
            document.getElementById('search').focus()
        }
        if(event.ctrlKey && event.key === 'm'){
            event.preventDefault()
            showForm.value = !showForm.value
        }
    })
})
</script>

<template>

    <input id="search" role="search" type="text" @input="handleChange">
    <div>
        <NewCopyPaste :class="showForm ? 'show':'noShow'" @add-command="handleNewCommand"/>
        <CopyPaste v-for="command in filter" :key="command.title" :command="command"/>
    </div>
</template>

<style>
#search {
    position: relative;
    border: none;
    border-radius: .2rem;
    left: 50%;
    transform: translate(-50%, -50%);

    padding: 5px 30px;
    font-size: 1.3rem;
    background-color: #f0f8ff1b;
    color: white;
}
    
.show {
    display: block;
}

.noShow {
    display: none;
}

</style>