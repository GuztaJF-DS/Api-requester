<script setup lang="ts">
import { toRefs } from 'vue';
import type { PropType } from 'vue'
const props = defineProps({
  header: {
    type:Array as PropType<String[][]>,
    required: true
  }
});

const { header } = toRefs(props);
console.log(header.value)

const emit = defineEmits(['update:value']);

function UpdateHeader(event: string, columnIndex: number, index:number){
  header.value[columnIndex][index]= event;
  emit('update:value', header)
}
</script>

<template>
  <div>
    <h2>Header Component</h2>
    <div class="header-input" v-for="(head, index) in header" :key="index">
      <input 
        v-bind:value="head[0]"
        v-on:input="UpdateHeader(($event.target as HTMLInputElement)?.value, index, 0)"
        type="text"
        placeholder="Header"
      />
      <input 
        v-bind:value="head[1]"
        v-on:input="UpdateHeader(($event.target as HTMLInputElement)?.value, index, 1)"
        type="text"
        placeholder="value"
      />
    </div>
    <button @click="$emit('update:value', 'testValue')">Make the Request</button>
  </div>
</template>

<style scoped lang="scss">
  input {
    background-color: transparent;
    border-radius: 2px;
    border: rgb(124, 124, 124) solid 1.2px;
    font-size: 19px;
    color: rgb(168, 168, 168);
    margin-bottom: 5px;
    :focus{
      outline: none;
    }
  }
  .header-input{
    display: flex;
  }
</style>