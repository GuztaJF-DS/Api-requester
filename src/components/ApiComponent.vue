<script async setup lang="ts">
import axios from 'axios';
import { ref, watch } from 'vue';
import type { Ref } from 'vue'
import HeaderField from './HeaderField.vue';

interface IFormsData {
  url: string;
  method: string;
  header: string[][];
  params: string;
}

const formsData: Ref<IFormsData> = ref({
  url: '',
  method: '',
  header: [['Content-Type','application/json']],
  params: ''
});
const apiData = ref();
async function Request() {
  try {
    const res = await axios({
      url: formsData.value.url,
      method: formsData.value.method
    });
    apiData.value = res.data;
  }
  catch (e) {
    console.error(e)
  }
}

watch(formsData.value, ({ header }) => {
  console.log('header',header)
})
</script>

<template>
  <div class="fields-area">
    <h1 class="green">Api Requester</h1>
    <div class="fields">
      <div class="url-field">
        <select v-model="formsData.method" class="method-select">
          <option disabled value="">Method</option>
          <option style="color:#a903fc;" value="get">GET</option>
          <option style="color:#23db02;" value="post">POST</option>
          <option style="color:#e6d200;" value="put">PUT</option>
          <option style="color:#e68200;" value="patch">PATCH</option>
          <option style="color:#db0d4e;" value="delete">DELETE</option>
        </select>
        <input v-model="formsData.url" placeholder="url" />
      </div>
      <HeaderField v-bind:header="formsData.header" v-on:update:value="formsData.header = $event" />
      <button @click="Request">Make the Request</button>
    </div>
  </div>
  <h3>
    {{ apiData }}
  </h3>
</template>

<style scoped lang="scss">
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.fields-area {
  width: 50%;
}

.url-field {
  display: flex;
  justify-content: space-between;

  >.method-select {
    width: 20%;
    height: 25px;
  }

  >input {
    width: 100%;
  }
}

.method-select {
  background-color: transparent;
  border: none;
  outline: none;
  border-bottom: rgb(124, 124, 124) solid 1.2px;
  border-bottom-left-radius: 7px;
  border-bottom-right-radius: 5px;
  color: rgb(192, 192, 192);

  :focus {
    outline: none;
    color: rgb(192, 192, 192);
  }

  option {
    background-color: #181818;
  }
}

input {
  background-color: transparent;
  border: none;
  border-bottom: rgb(124, 124, 124) solid 1.2px;
  border-bottom-left-radius: 5px;
  border-bottom-right-radius: 7px;
  font-size: 19px;
  color: rgb(168, 168, 168);
  margin-bottom: 5px;

  :focus {
    outline: none;
  }
}

.greetings {
  text-align: left;
}

.fields {
  display: flex;
  flex-direction: column;
}</style>
