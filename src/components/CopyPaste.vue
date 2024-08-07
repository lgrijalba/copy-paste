<script lang="ts" setup>
import { onMounted, ref } from 'vue'
import type {Command} from '../types'
const props = defineProps(['command'])
const {title, command, description} = props.command
const contentToCopy = ref()
const words = command?.split(" ");

const handleClick = () => {
  const text = contentToCopy.value.innerText
  navigator.clipboard.writeText(text)
}
</script>

<template>
    <div class="component">
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
    </div>
</template>

<style scoped>

  .component {
      margin: 10px 130px;
      padding: 1rem;
      font-size: 20px;
      background-color: rgba(77, 77, 77, 0.201);
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

  .copy-button {
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
</style>
