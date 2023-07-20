<script async setup lang="ts">
import axios from 'axios';
import type { AxiosError } from 'axios';
import { ref, onMounted } from 'vue';
import type { Ref } from 'vue'
import MyApi from '../assets/MyApi.json';
import HeaderField from './HeaderField.vue';
import ParamsField from './ParamsField.vue';

interface IFormsData {
  url: string;
  method: string;
  headers: string[][];
  params: string;
}

const formsData: Ref<IFormsData> = ref({
  url: 'https://catfact.ninja/fact',
  method: 'get',
  headers: [['Content-Type', 'application/json']],
  params: '{}'
});
const ErrorData = ref();
const apiData = ref();
async function Request() {
  try {
    const headers: { [key: string]: string } = {};
    formsData.value.headers.forEach(([key, value]) => {
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
  catch (e: AxiosError | any) {
    console.error(e?.response?.data?.message)
    ErrorData.value = e?.response?.data?.message || e;
  }
}
const windowValue = ref(false);
const displaySideBar = ref(false);
const ulList = ref();
const myApiOptions = MyApi;

function setApiProps(selectedItem: any) {
  formsData.value = selectedItem
}

function updateSideBar() {
  displaySideBar.value = true;
  ulList.value.focus();
}

function getColor(method:string){
  switch(method){
    case 'get':
      return 'color: #a903fc';
    case 'post':
      return 'color: #23db02';
    case 'put':
      return 'color: #e6d200';
    case 'patch':
      return 'color: #e68200';
    case 'delete':
      return 'color: #db0d4e';
    default:
      return 'color: #f8f8f8';
  }
}
</script>

<template>
  <button class="display-button" @click="updateSideBar();">
    My API
  </button>
  <ul ref="ulList" @blur="displaySideBar = false" tabindex="0" :style="[
    !displaySideBar
      ? { 'width': '0%' }
      : { 'width': '220px', 'filter': 'blur(0px)'  }
  ]" class="api-side-bar">

    <button class="exit-side-bar-button">
      Close
    </button>
    <li class="li-side-bar" v-for="(item, index) in myApiOptions" :key="index">
      {{ item.name }}
        <div class="div-side-bar" v-for="(subitem, subindex) in item.requests" :key="subindex" @click="setApiProps(subitem)">
          <div :style="getColor(subitem.method)">{{ subitem.method }}</div> {{ subitem.name }}
        </div>
    </li>
  </ul>
  <div class="api-container" :style="[
    !displaySideBar
      ? { 'filter': 'blur(0px)' }
      : { 'filter': 'blur(3px)' }
  ]">
    <div class="fields-area">
      <h1 class="green-h1">Api Requester</h1>
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
            <HeaderField v-bind:header="formsData.headers" v-on:update:value="formsData.headers = $event" />
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

.green-h1{
  color:#00bd7e;
  @media (max-width: 280px){
    font-size: 14.8vw;
  }
}

.api-side-bar {
  background-color: #008b5d;
  position: absolute;
  overflow: hidden;
  height: 100vh;
  white-space: nowrap;
  z-index: 5;
  transition: 0.4s width;
  *{
    transition: none;

  }
  margin: 0;
  padding: 0;
  padding-top: 2px !important;
}

.display-button {
  position: absolute;
  background-color: transparent;
  border: #008b5d 1px solid;
  border-radius: 5px;
  color: white;
  z-index: 3;
  height: 20px;
  width: 74px;
  margin: 5px;
}

.exit-side-bar-button {
  background-color: transparent;
  border: white 1px solid;
  border-radius: 5px;
  color: white;
  z-index: 3;
  height: 20px;
  width: 74px;
  margin-left: 5px;
  padding: 0px 19px;
}

.li-side-bar {
  color: #f8f8f8;
  padding: 0px 11px;
  cursor: default;
  margin-top: 10px;
    text-shadow: 
    -1px -1px 5px #181818, 
    -1px 1px 5px #181818,                    
    1px -1px 5px #181818,                  
    1px 0px 5px #181818;

  * {
    cursor: pointer;
  }
}

.div-side-bar{
  display:flex;
  flex-direction: row;
  :first-child{
    width: 48px;
  }
}

.api-container {
  display: flex;
  justify-content: space-between;
  height: 90vh;
  margin: 0 auto;
  max-width: 1280px;
  padding: 2rem;
  transition: 0.4s filter ;
  @media (max-width: 688px){
    flex-direction: column;
    height: 150vh;
  }
}

.fields-area {
  min-width: 120px;
  width: 45%;
  @media (max-width: 688px){
    width: 100%;
  }
}

.api-data-textarea {
  width: 45%;
  margin-top: 2.5%;
  height: 100%;
  @media (max-width: 688px){
    width: 100%;
  }
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
  min-width: 60px;
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
