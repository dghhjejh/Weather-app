<template>
    <div class="container p-0">
        <div class="d-flex">
            <div class="card main-div w-100">
                <div class="p-3">
                    <h2 class="mb-1 day">Today</h2>
                    <p class="text-light date mb-0">{{ date }}</p>
                    <small>{{ time }}</small>
                    <h2 class="place">{{ name }} <small>{{ country }}</small></h2>
                    <div class="temp">
                        <h1 class="weather-temp">{{ temperature }}&deg;</h1>
                        <h2 class="text-light">{{ description }} <img :src="iconUrl"></h2>
                    </div>
                </div>
            </div>
            <div class="card card-2 w-100">
            <table class="m-4">
                <tbody>
                    <tr>
                        <th>Sea level</th>
                        <th v-if="sea_level>0">{{ sea_level }}</th>
                        <th v-else></th>
                    </tr>
                    <tr>
                        <th>Humidity</th>
                        <th>{{ humidity }}</th>
                    </tr>
                    <tr>
                        <th>Wind</th>
                        <th>{{ wind }}</th>
                    </tr>
                </tbody>
            </table>
            <DaysWeather :cityName ="cityName"/>
            <div id="div_form" class="d-flex m-3 justify-content-center">
                <form action="">
                    <input type="button" value="Change Location" @click="changeLocation" class="btn change_btn btn-primary">
                </form>
            </div>
        </div>
        </div>
    </div>
    <div>
</div>
</template>

<script setup>
import 'font-awesome/css/font-awesome.min.css';
import DaysWeather from './DaysWeather.vue';
import {ref, onMounted, defineProps } from 'vue';
import moment from "moment-timezone";
import axios from "axios";
const props = defineProps({
    city:{
        type: String,
        required: true
    }
});

const humidity = ref(null);
const sea_level = ref(null);
const wind = ref(null);
const country = ref(null);
const temperature = ref(null);
const description = ref(null);
const date = ref(null);
const time = ref(null);
const name = ref(null)
const d = new Date()
const iconUrl = ref(null)
const weatherData = ref({});
const ville = ref(props.city);
const cityName = ville.value;
const monthNames = [
  "Janvier",
  "Février",
  "Mars",
  "Avril",
  "Mai",
  "Juin",
  "Juillet",
  "Août",
  "Septembre",
  "Octobre",
  "Novembre",
  "Décembre"
]
const getCountryDateTime = (countryCode) =>{
    const timezone = moment.tz.zonesForCountry(countryCode)[0];
    const datetime = moment().tz(timezone);
    return {
        date: datetime.format("YYYY-MM-DD"),
        time: datetime.format("HH:mm")
    }

}
const created = async() =>{
    try{
        if(ville.value != ""){
            const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${ville.value}&units=metric&appid=278228bc163d83266e4ae64ab63c58a3`)
            weatherData.value = response.data;
            iconUrl.value = `https://api.openweathermap.org/img/w/${weatherData.value.weather[0].icon}.png`
            temperature.value = Math.round(weatherData.value.main.temp);
            date.value = `${d.getDate()} ${monthNames[d.getMonth()]} ${d.getFullYear()}`;
            description.value = weatherData.value.weather[0].description;
            country.value = weatherData.value.sys.country;
            time.value = d.getHours() + " : " + d.getMinutes();
            if (country.value != null){
                time.value = getCountryDateTime(country.value).time;
                date.value = getCountryDateTime(country.value).date;
            }
            name.value = weatherData.value.name;
            wind.value = weatherData.value.wind.speed;
            sea_level.value = weatherData.value.main.sea_level;
            humidity.value = weatherData.value.main.humidity;
            console.log(weatherData.value)

        }
       
    }catch(e){
        console.error(e);
    }
   
}
onMounted(() => {
    created()
})
const changeLocation = () =>{
    window.location.reload();
}
</script>

<style scoped>
.weather-temp{
    margin:0;
    font-weight: 700;
    font-size: 4em;
}
h2.mb-1.day{
    font-size: 3rem;
    font-weight: 400;
}

.main-div{
    border-radius: 20px;
    color: #fff;
    background-image: url("https://images.unsplash.com/photo-1579003593419-98f949b9398f?w=1000&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8Mnx8d2VhdGhlcnxlbnwwfHwwfHx8MA%3D%3D");
    background-size: cover;
    background-position: center;
    background-blend-mode: overlay;
    background-color: rgba(0, 0, 0, 0.5);
    background-repeat: no-repeat;
}

.temp{
    position: absolut;
    bottom: 0;
}

.main-div:hover{
    transform: scale(1.1);
    transition: transform 0.5s ease;
    z-index: 1;
}
.card-2{
    background-color: #212730;
    border-radius: 20px;
}
.card-details{
    margin-left: 19px;
}
.h1_left{
    position: absolute;
    bottom: 25px;
    left: 16px;
    font-size: 3vw;
    line-height: 1.2;
}
.h3-left{
    position: absolute;
    left: 16px;
    font-size: 2vw;
    line-height: 0.5;
}
.h3_left{
    font-size: 1rem;
}

table{
    position: relative;
    left: 15px;
    border-collapse: separate;
    border-spacing: 15px;
    width: 85%;
    text-align: left;
    max-width: 600px;
    margin: 0 auto;
}

th,
td{
    font-size: 18px;
    color: #fff;
}

td{
    text-align: right;
}


table,
tr:hover{
    color: red;
}

.change_btn{
    background-image: linear-gradient(to right, cyan);
}

.change_btn:hover{
    transform: scale(0.9);
    transition: transform 0.1s ease;
}
</style>