<script async setup lang="ts">
import axios from 'axios';
import { ref } from 'vue';
import type { Ref } from 'vue'
import HeaderField from './HeaderField.vue';
import ParamsField from './ParamsField.vue';

interface IFormsData {
  url: string;
  method: string;
  header: string[][];
  params: string;
}

const formsData: Ref<IFormsData> = ref({
  url: 'https://catfact.ninja/fact',
  method: 'get',
  header: [['Content-Type', 'application/json']],
  params: '{}'
});
const ErrorData = ref();
const apiData = ref();
async function Request() {
  try {
    const headers: { [key: string]: string } = {};
    formsData.value.header.forEach(([key, value]) => {
      headers[key] = value;
    });
    ErrorData.value = '';
    const requestParams = {
      url: formsData.value.url,
      method: formsData.value.method,
      headers: headers,
      data: JSON.parse(formsData.value.params),
    }
    const res = await axios(requestParams);
    apiData.value = JSON.stringify(res.data, undefined, 4);
  }
  catch (e) {
    console.error(e)
    ErrorData.value = e;
  }
}

// watch(formsData.value, ({header}) => {
//   console.log('header', header)
// })
const windowValue = ref(false);
const displaySideBar = ref(true);
const myApiOptions = [{name:'item1'}, {name:'item2'}];
</script>

<template>
  <button class="display-button" @click = "displaySideBar = !displaySideBar">
    My APIs {{ displaySideBar ? '<':'>' }}
  </button>
  <ul 
    :style="[
      !displaySideBar 
      ? { 'width': '0%', 'padding': '0px', 'padding-top' : '25px' }
      : { 'width': '220px', 'padding': '11px', 'padding-top': '25px' }
    ]"
    class="api-side-bar"
  >
  <li 
    v-for="(item, index) in myApiOptions"
    :key="index"
    @click="console.log('item.name')"

  >
    {{ item.name }}
  </li>
  </ul>
  <div class="api-container">
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
        <div class="button-div">
          <button class="window-button"
            :style="[!windowValue ? { 'background': '#114c31' } : { 'background': '#181818' }]"
            @click="windowValue = false;">
            Params
          </button>
          <button class="window-button" :style="[windowValue ? { 'background': '#114c31' } : { 'background': '#181818' }]"
            @click="windowValue = true;">
            Header
          </button>
        </div>
        <div class="window-div">
          <div v-if="windowValue">
            <HeaderField v-bind:header="formsData.header" v-on:update:value="formsData.header = $event" />
          </div>
          <div v-else>
            <ParamsField v-bind:params="formsData.params" v-on:update:value=" formsData.params = $event" />
          </div>
        </div>
        <button class="request-button" @click="Request">Make the Request</button>
        {{ ErrorData }}
      </div>
    </div>
    <textarea v-model="apiData" class="api-data-textarea">
    </textarea>
  </div>
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

textarea {
  background-color: #4d4d4d;
  border-color: transparent;
  color: #dfdfdf;
  resize: none;
}

.api-side-bar{
  background-color: #008b5d;
  position: absolute;
  overflow: hidden;
  height: 100vh;
  z-index: 5;
  transition: 0.4s width;
  margin: 0;
}

.display-button{
  position: absolute;
  background-color: transparent;
  border:#008b5d 1px solid;
  border-radius: 5px;
  color:white;
  z-index: 6;
  margin: 5px;
}

.api-container {
  display: flex;
  justify-content: space-between;
  height: 90vh;
  margin: 0 auto;
  max-width: 1280px;
  padding: 2rem;
}

.fields-area {
  width: 45%;
}

.api-data-textarea {
  width: 45%;
  margin-top: 2.5%;
  height: 100%;
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

.fields {
  display: flex;
  flex-direction: column;

  ::-webkit-scrollbar {
    width: 2px;
  }

  ::-webkit-scrollbar-track {
    background: transparent;
  }

  ::-webkit-scrollbar-thumb {
    background-color: #666666;
    outline: 1px solid #666666;
  }
}

.button-div {
  display: flex;
  margin-top: 15px;

  :first-child {
    border-top-left-radius: 5px;
  }

  :last-child {
    border-top-right-radius: 5px;
  }
}

.window-div {
  min-height: 40vh;
  max-height: 60vh;
  overflow-y: auto;
  border: #666666 1px solid;
}


.window-button {
  width: 20%;
  background-color: transparent;
  color: #C0C0C0;
  border: #666666 1px solid;
}

.method-select {
  background-color: transparent;
  border: none;
  outline: none;
  border-bottom: #666666 solid 1.2px;
  border-bottom-left-radius: 7px;
  border-bottom-right-radius: 5px;
  color: #C0C0C0;

  :focus {
    outline: none;
    color: #C0C0C0;
  }

  option {
    background-color: #181818;
  }
}

input {
  background-color: transparent;
  border: none;
  border-bottom: #666666 solid 1.2px;
  border-bottom-left-radius: 5px;
  border-bottom-right-radius: 7px;
  font-size: 19px;
  color: #999999;
  margin-bottom: 5px;

  :focus {
    outline: none;
  }
}

.greetings {
  text-align: left;
}


.request-button {
  margin-top: 20px;
  background-color: #00bd7e;
  color: #181818;
  font-weight: 600;
  border: #008b5d solid 1.2px;
  padding: 3px;
  font-size: 14px;
  border-radius: 5px;
}
</style>
