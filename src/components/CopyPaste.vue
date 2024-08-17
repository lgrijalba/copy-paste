<script lang="ts" setup>
import { ref } from 'vue'
import type { PropType } from 'vue'
import type {Command} from '../types'
const props = defineProps({
  command: {
    type: Object as PropType<Command>,
    required: true
  }
})

const emit = defineEmits({ deleteCommand: String });

const {title, command, description} = props.command
const contentToCopy = ref()
const words = command?.split(" ");

const handleClick = () => {
  const text = contentToCopy.value.innerText
  navigator.clipboard.writeText(text)
}
</script>

<template>
    <div class="component" draggable="true">
        <h5 class="title">{{title}}</h5>

        <div class="wrapper">
            <p class="content-to-copy" ref="contentToCopy">
                <template v-for="word in words">
                    <code v-if="word.startsWith('~~~')"contenteditable="true" class="editable-text">
                        {{word.substring(3)}}
                    </code>
                    <code v-else>
                        {{word}}
                    </code>&thinsp;
                </template>
            </p>
            <button class="copy-button" @click="handleClick">
            <svg
                xmlns="http://www.w3.org/2000/svg"
                width="27"
                height="27"
                fill="none"
                viewBox="0 0 24 24"
                class="icon-sm"
                ><path
                fill="currentColor"
                fill-rule="evenodd"
                d="M7 5a3 3 0 0 1 3-3h9a3 3 0 0 1 3 3v9a3 3 0 0 1-3 3h-2v2a3 3 0 0 1-3 3H5a3 3 0 0 1-3-3v-9a3 3 0 0 1 3-3h2zm2 2h5a3 3 0 0 1 3 3v5h2a1 1 0 0 0 1-1V5a1 1 0 0 0-1-1h-9a1 1 0 0 0-1 1zM5 9a1 1 0 0 0-1 1v9a1 1 0 0 0 1 1h9a1 1 0 0 0 1-1v-9a1 1 0 0 0-1-1z"
                clip-rule="evenodd"></path></svg>
            </button>
        </div>

        <p class="description">{{description}}</p>

        <button id="delete-button" @click="emit('deleteCommand', title)">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash3" viewBox="0 0 16 16">
            <path d="M6.5 1h3a.5.5 0 0 1 .5.5v1H6v-1a.5.5 0 0 1 .5-.5M11 2.5v-1A1.5 1.5 0 0 0 9.5 0h-3A1.5 1.5 0 0 0 5 1.5v1H1.5a.5.5 0 0 0 0 1h.538l.853 10.66A2 2 0 0 0 4.885 16h6.23a2 2 0 0 0 1.994-1.84l.853-10.66h.538a.5.5 0 0 0 0-1zm1.958 1-.846 10.58a1 1 0 0 1-.997.92h-6.23a1 1 0 0 1-.997-.92L3.042 3.5zm-7.487 1a.5.5 0 0 1 .528.47l.5 8.5a.5.5 0 0 1-.998.06L5 5.03a.5.5 0 0 1 .47-.53Zm5.058 0a.5.5 0 0 1 .47.53l-.5 8.5a.5.5 0 1 1-.998-.06l.5-8.5a.5.5 0 0 1 .528-.47M8 4.5a.5.5 0 0 1 .5.5v8.5a.5.5 0 0 1-1 0V5a.5.5 0 0 1 .5-.5"/>
          </svg>
        </button>
    </div>
</template>

<style scoped>

  .component {
      position: relative;
      margin: 10px 60px 10px 10px;
      padding: 1rem;
      font-size: 20px;
      background-color: rgba(77, 77, 77, 0.201);
  }

  @media (min-width: 1000px) {
    .component{
      margin: 10px 130px;
      padding: 1rem;
    }
  }

  main {
    margin: 0 130px;
    padding: 1rem;
    font-size: 20px;
  }

  h1 {
    font-size: 4rem;
    font-weight: 700;
    line-height: 1;
    text-align: center;
    margin-bottom: 1em;
  }
  .text-gradient {
    background-image: var(--accent-gradient);
    /* -webkit-background-clip: text; */
    -webkit-text-fill-color: transparent;
    background-size: 400%;
    background-position: 0%;
  }

  .wrapper {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    background-color: #636363;
    padding: 0 10px;
    border-radius: 0.8rem;
    color: white;
    height: 43px;
    font-size: 1rem;
    padding: 0 30px;
  }

  button {
    background: none;
    border: none;
    cursor: pointer;
    padding: 0;
    margin: 0;
    color: unset;

    display: flex;
    align-items: center;
  }

  .content-to-copy {
    padding: 0;
    margin: 0;
  }

  .editable-text {
    background-color: #d9d9d967;
    border-radius: 4px;
    padding: 2px 5px 1px 5px;
  }

  .title,
  .description {
    margin: 0 0 0 30px;
  }

  .title {
    color: #ffffff;
  }

  .description {
    font-size: 0.8rem;
  }

  #delete-button {
    transition: .3s all;
    position: absolute;
    right: -30px;
    top: 0;
    height: 100%;

    &:hover {
      color: #ff6767;
    }
  }
</style>
