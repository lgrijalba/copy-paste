<script setup lang="ts">
import { ref, onMounted } from 'vue'
import type { PropType } from 'vue';
import type { Command } from '../types'
import CopyPaste from './CopyPaste.vue'

defineOptions({ inheritAttrs: false })

const {commands} = defineProps({
    commands: {
        type: Array as PropType<Command[]>,
        required: true
    }
})
let filter = ref([...commands])

const handleChange = (event) => {
    const newSearch = event.target.value
    filter.value = commands
    if(newSearch){
        filter.value = commands.filter((command) => {
            return command.title?.toUpperCase().includes(newSearch?.toUpperCase()) 
            || command.description?.toUpperCase().includes(newSearch?.toUpperCase()) 
            || command.command?.toUpperCase().includes(newSearch?.toUpperCase()) 
        })  
    }
}

onMounted(() => {
    document.addEventListener('keydown', (event) => {
        if(event.ctrlKey && event.key === 'f'){
            event.preventDefault()
            document.getElementById('search').focus()
        }
    })
})

</script>

<template>

    <input id="search" role="search" type="text" @input="handleChange">
    <div>
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
    

</style>