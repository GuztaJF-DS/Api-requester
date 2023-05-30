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

const emit = defineEmits(['update:value']);

function updateHeader(event: string, columnIndex: number, index:number){
  header.value[columnIndex][index] = event;
  emit('update:value', header)
}

function addHeader() {
  header.value.push(['',''])
}

function removeHeader(index: number){
  if(index!==0){
    header.value.splice(index, index);
  }
  else{
    header.value.shift();
  }
  emit('update:value', header)
}

</script>

<template>
  <div>
    <h2>Header Component</h2>
    <div class="header-input" v-for="(head, index) in header" :key="index">
      <input 
        v-bind:value="head[0]"
        v-on:input="updateHeader(($event.target as HTMLInputElement)?.value, index, 0)"
        type="text"
        placeholder="Header"
      />
      <input 
        v-bind:value="head[1]"
        v-on:input="updateHeader(($event.target as HTMLInputElement)?.value, index, 1)"
        type="text"
        placeholder="value"
      />
      <button class="remove-button" @click="removeHeader(index)">X</button>
    </div>
    <button @click="addHeader()">Add Header</button>
  </div>
</template>

<style scoped lang="scss">
  h2 {
    color:#acacac;
  }

  input {
    background-color: transparent;
    border-radius: 2px;
    border: #666666 solid 1.2px;
    font-size: 19px;
    color: #999999;
    height: 28px;
    margin-bottom: 5px;
    :focus{
      outline: none;
    }
  }
  .header-input{
    display: flex;
  }
  
  button {
    background-color: transparent;// #00CC99
    color: #FFFFFF;
    border: #666666 solid 1.2px;
    border-radius: 5px;
    padding: 3px 8px;
  }
  .remove-button{
    border-top-left-radius: 0px;
    height: 28px;
    background-color: #C91C1C;
    border-color: #ca4242;
    padding: 5px;
  }

</style>