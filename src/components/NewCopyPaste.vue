<script setup lang="ts">
import { ref, defineEmits } from 'vue'
import type { Command } from '../types'

const emit = defineEmits({
    addCommand: (command:Command) => true
});

const inputTitle = ref('')
const inputCommand = ref('')
const inputDescription = ref('')

const addCommand = (e) => {
    e.preventDefault()
    const isFullData = inputTitle.value !== '' 
                    && inputCommand.value !== '' 
                    && inputDescription.value !== '' 
    
    if(isFullData){
        const newCommand:Command = {
            title: inputTitle.value,
            command: inputCommand.value,
            description: inputDescription.value
        }
        
        inputTitle.value = ''
        inputCommand.value = ''
        inputDescription.value = ''
        emit('addCommand', newCommand)
    }
    
}
</script>

<template>
    <div class="wrapper">
        <form @submit="addCommand">
                <div>
                    <h5>Title</h5>
                    <input v-model="inputTitle" type="text">
                </div>

                <div>
                    <h5>Command: words starting with ~~~ will be editable</h5>
                    <input v-model="inputCommand" type="text">
                </div>

                <div>
                    <h5>Description</h5>
                    <input v-model="inputDescription" type="text" >
                </div>

                <button hidden type="submit"></button>
        </form>

    </div>
</template>

<style scoped>

*{
    box-sizing: border-box;
}

.wrapper {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 0);

    padding: 3rem; 
    font-size: 20px;
    background-color: #dfdfdfe5;
    border-radius: .3rem;
}

form{
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 10px;
}

input{
    width: 100%;
    margin: 0;
    padding: 0;
    border: none;
    border-radius: .2rem;
    padding: 5px 10px;
    background-color: #636363;
    color: white
}

h5{
    margin: 0;
    padding: 0;
    font-size: 1rem;
    color: #636363;
}

    
</style>