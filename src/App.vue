<template>
  <div id="app">
    <div class="header container h-100 p-5">
      <h1 class="mb-5">Wheather App</h1>
      <div class="d-flex justify-content-center h-100">
        <div class="searchbar w-50 mx-2">
        <input type="text" class="input from-control" v-model="city" placeholder="Enter city name">
        </div>
        <button class="btn-search btn btn-primary" @click="searchWeather">search</button>
    </div>
    </div>
    <PrevisionsSemaine :city="city" v-if="showWeather"/>
  </div>
</template>

<script setup>
import {ref, nextTick} from "vue";
import PrevisionsSemaine from "./components/PrevisionsSemaine.vue";
const city = ref("");
const showWeather = ref(false);
const searchWeather = async() =>{
    showWeather.value = false;
    await nextTick();
    showWeather.value = true;
}
</script>

<style>
body{
  background-color: #121212 !important;
}
.header{
  background-color: #212730;
  border-radius: 20px;
  color: #fff;
  text-align:center;
  font-family:'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  margin-top: 5rem;
}
.btn-search{
    background-image: linear-gradient(to right, cyan, magenta);
}
</style>
