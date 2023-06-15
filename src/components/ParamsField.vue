<script setup lang="ts">
import { toRefs } from 'vue';
const props = defineProps({
  params: {
    type: String,
    required: true
  }
});

const { params } = toRefs(props);

const emit = defineEmits(['update:value']);

function updateHeader(event: string){
  emit('update:value', event)
}

function insertTab(event: KeyboardEvent): void {
    if (event.key === 'Tab') {
      event.preventDefault();

      const textarea = event.target as HTMLTextAreaElement;
      const start = textarea.selectionStart as number;
      const end = textarea.selectionEnd as number;

      const tab = "\t";
      params.value =
        params.value.substring(0, start) + tab +
        params.value.substring(end);

      textarea.selectionStart = textarea.selectionEnd = start + tab.length;
    }
  }
</script>

<template>
  <div class="main-container">
    <h2>Params Component
    </h2>
      <textarea 
        v-bind:value="params"
        v-on:input="updateHeader(($event.target as HTMLInputElement)?.value)"
        placeholder="Header"
        class="params-textarea"
        @keydown.tab.prevent="insertTab"
      >
    </textarea>
  </div>
</template>

<style scoped lang="scss">
  .main-container{
    padding: 0px 5px;
  }

  h2 {
    color:#acacac;
    position: sticky;
    top: 0;
    background-color: #181818;
    z-index: 3;
    display: flex;
    flex-direction: column;
    padding-bottom: 5px;
  }

  .params-textarea {
    background-color: transparent;
    border-radius: 2px;
    border: #666666 solid 1.2px;
    color: #999999;
    height: 300px;
    width: 100%;
    margin-bottom: 5px;
    margin-right: 3px;
    resize: none;
    :focus{
      outline: none;
    }
  }
</style>