<template>
    <div class="days-tab text-center">
        <div v-if="loading" class="loading">loading ...</div>
        <ul v-else class="p-0">
            <li v-for="day in foreCast" :key="day.date" class="li_active">
                <div class="py-3"><img :src="day.iconUrl"></div>
                <div class="py-3">{{ getDayName(day.date) }}</div>
                <div class="py-3">{{ day.temperature }}&deg;C</div>
            </li>
        </ul>
    </div>
</template>
<script setup>
import {ref, onMounted, defineProps} from "vue"
import axios from "axios";
import moment from "moment";
const props = defineProps({
    cityName: String
})
const foreCast = ref([])
const loading = ref(true)

const cityName = ref(props.cityName)
const forecastData = ref({})
const filteredData = ref({})
const apikey = ref("278228bc163d83266e4ae64ab63c58a3");
const apiURL = `https://api.openweathermap.org/data/2.5/forecast?q=${cityName.value}&units=metric&appid=${apikey.value}` 
console.log(cityName)
const getWeatherData = async() =>{
    try{
        const response = await axios.get(apiURL);
        forecastData.value = response.data.list;
        filteredData.value = forecastData.value.map(item =>{
            return{
                date: moment(item.dt_txt.split(" ")[0]),
                temperature: Math.round(item.main.temp),
                description: item.weather[0].description,
                iconUrl: `https://api.openweathermap.org/img/w/${item.weather[0].icon}.png`
            }
        }).reduce((acc, item) =>{
            if(!acc.some(day => day.date.isSame(item.date, "day"))){
                acc.push(item)
            }
            return acc;
        }, []).slice(0, 5);
        console.log(filteredData.value)
        foreCast.value = filteredData.value;
        loading.value = false;
    }catch(e){
        console.error(e);
    }
    
}
const getDayName = (date) =>{
        return date.format("ddd");
    }
onMounted(() => {
    getWeatherData();
})
</script>

<style>
.days-tab{
    width: 90%;
    box-shadow: 0 4px 0 rgba(0, 0, 0, 0.2), 0 6px 20px rgba(0, 0, 0, 0.19);
    border-radius: 20px;
    width: 90%;
    margin: auto;
}

.loading{
    color: #fff;
}
ul{
    margin: 0;
}

li{
    display: inline-block;
    list-style: none;
    height: 100%;
    width: 21%;
    max-width: 21%;
    font-size: 1vw;
    line-height: 1.2;
}

span{
    display: block;
    margin-bottom: 5px;
    font: 100% sans-serif;
    height: 35px;
}
.li_active{
    background-color: #253d5c;
    color: #222831;
    border-radius: 10px;
    margin: 0.5rem;
    color: #fff;
    font-weight: 600;
}

.li_active:hover{
    transform: scale(1.2);
    transition: transform 0.1s ease;
}

.li_active_temp{
    display: inline-block;
    background-color: #222831;
    color: #ffffff;
    transition: background-color 0.5s;
    border-radius: 10px;
}

.li_active_temp:hover{
    transform: scale(1.2);
    transition: transform 0.1s ease;
    background: #fff;
    border-radius: 10px;
    color: #191a1f;
}

</style>