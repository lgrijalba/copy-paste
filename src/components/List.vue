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

const handleDeleteCommand = (title) => {
    const storageCommands = readStorage()
    const newCommands = storageCommands.filter((c: Command) => c.title !== title)
    saveStorage(newCommands)
    filter.value = newCommands
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

const handleDownload = () => {
    const a = document.createElement("a");
    a.style.display = "none";
    document.body.appendChild(a);

    const file = new File([JSON.stringify(readStorage())], "fileName.copypaste", { type: "text/json"})
    a.href = window.URL.createObjectURL(file);

    let fileName = prompt("Collection Name:", "commands");
    
    if (fileName){
        a.setAttribute("download", fileName + ".copypaste");
        a.click();

        window.URL.revokeObjectURL(a.href);
        document.body.removeChild(a);
    }
}

const handleLoadFile = (event) => {
    const [file] = event.target.files
    if(file){
        const reader = new FileReader()
        reader.onload = (e) => {
            const commands = JSON.parse(e.target.result.toString())
            saveStorage(commands)
            filter.value = commands

            event.target.value = ''
        }
        reader.readAsText(file)
    }
}

onMounted(() => {
    const storageCommands = readStorage()
    if(storageCommands.length > 0) filter.value = storageCommands
    else saveStorage(commands)

    document.addEventListener('keydown', (event) => {    
        if(event.key === 'Escape'){
            event.preventDefault()
            showForm.value = false
        }
        
        if(event.ctrlKey && event.key === 'f'){
            event.preventDefault()
            document.getElementById('search').focus()
        }
        if(event.ctrlKey && event.key === 'm'){
            event.preventDefault()
            showForm.value = !showForm.value
        }
        if(event.ctrlKey && event.key === 's'){
            event.preventDefault()
            handleDownload()
        }
    })
})
</script>

<template>

    <header>
        <input id="search" role="search" type="text" @input="handleChange">

        <button @click="showForm = !showForm">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" class="bi bi-plus-circle" viewBox="0 0 16 16">
                <path d="M8 15A7 7 0 1 1 8 1a7 7 0 0 1 0 14m0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16"/>
                <path d="M8 4a.5.5 0 0 1 .5.5v3h3a.5.5 0 0 1 0 1h-3v3a.5.5 0 0 1-1 0v-3h-3a.5.5 0 0 1 0-1h3v-3A.5.5 0 0 1 8 4"/>
            </svg>
        </button>
        <label for="file" id="label-file">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" class="bi bi-cloud-upload" viewBox="0 0 16 16">
                <path fill-rule="evenodd" d="M4.406 1.342A5.53 5.53 0 0 1 8 0c2.69 0 4.923 2 5.166 4.579C14.758 4.804 16 6.137 16 7.773 16 9.569 14.502 11 12.687 11H10a.5.5 0 0 1 0-1h2.688C13.979 10 15 8.988 15 7.773c0-1.216-1.02-2.228-2.313-2.228h-.5v-.5C12.188 2.825 10.328 1 8 1a4.53 4.53 0 0 0-2.941 1.1c-.757.652-1.153 1.438-1.153 2.055v.448l-.445.049C2.064 4.805 1 5.952 1 7.318 1 8.785 2.23 10 3.781 10H6a.5.5 0 0 1 0 1H3.781C1.708 11 0 9.366 0 7.318c0-1.763 1.266-3.223 2.942-3.593.143-.863.698-1.723 1.464-2.383"/>
                <path fill-rule="evenodd" d="M7.646 4.146a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1-.708.708L8.5 5.707V14.5a.5.5 0 0 1-1 0V5.707L5.354 7.854a.5.5 0 1 1-.708-.708z"/>
            </svg>
            <input type="file" name="file" id="file" accept=".copypaste" @change="handleLoadFile">
        </label>
        <button id="download-file" @click="handleDownload">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" class="bi bi-cloud-download" viewBox="0 0 16 16">
                <path d="M4.406 1.342A5.53 5.53 0 0 1 8 0c2.69 0 4.923 2 5.166 4.579C14.758 4.804 16 6.137 16 7.773 16 9.569 14.502 11 12.687 11H10a.5.5 0 0 1 0-1h2.688C13.979 10 15 8.988 15 7.773c0-1.216-1.02-2.228-2.313-2.228h-.5v-.5C12.188 2.825 10.328 1 8 1a4.53 4.53 0 0 0-2.941 1.1c-.757.652-1.153 1.438-1.153 2.055v.448l-.445.049C2.064 4.805 1 5.952 1 7.318 1 8.785 2.23 10 3.781 10H6a.5.5 0 0 1 0 1H3.781C1.708 11 0 9.366 0 7.318c0-1.763 1.266-3.223 2.942-3.593.143-.863.698-1.723 1.464-2.383"/>
                <path d="M7.646 15.854a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 14.293V5.5a.5.5 0 0 0-1 0v8.793l-2.146-2.147a.5.5 0 0 0-.708.708z"/>
            </svg>
        </button>
    </header>
    <div>
        <NewCopyPaste :class="showForm ? 'show':'noShow'" @add-command="handleNewCommand"/>
        <CopyPaste v-for="command in filter" :key="command.title" :command="command" @delete-command="handleDeleteCommand"/>
    </div>
</template>

<style scoped>
#search {
    border: none;
    border-radius: .2rem;

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

input[type="file"] {
    display: none;
}

header {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
    margin: 20px 0;
}



button, #label-file {
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;

    background-color: transparent;
    color: unset;
    border: none;
}

</style>