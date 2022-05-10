<template>
<div class="main" :key="this.getWeatherStatus ? this.getWeatherStatus : 'key2'" v-bind:style="(this.getWeatherStatus==='sunny') ? { 'background-image': 'url(' + '/assets/sunnygif.gif'  + ')'} :
 (this.getWeatherStatus==='clouds') ? { 'background-image': 'url('+  '/assets/cloudy.gif' + ')' }  : 
 (this.getWeatherStatus==='snow') ? { 'background-image': 'url('+  '/assets/snow.gif' + ')' } : 
 (this.getWeatherStatus==='clear') ? { 'background-image': 'url('+  '/assets/clear.gif' + ')' } :
 (this.getWeatherStatus==='thunder') ? { 'background-image': 'url('+  '/assets/thunder.gif' + ')' } :
 (this.getWeatherStatus==='rainy') ? { 'background-image': 'url('+  '/assets/rain.gif' + ')' } :


 { 'background-image': 'url('+  '/assets/background.jpeg' + ')' }">
    <div class="left">
       {{getWeatherImage}}
        <div class="Intro">
            <div class="heading"><b>{{this.time}}</b></div>
            <div class="date"><b>{{this.date}}</b></div>
            <div v-if="this.wishMessage=='Good morning'" class="wishMessage">
                <img src="@/assets/Goodmorining.jpeg" alt="goodmorning" class="wishImage">
                <div class="heading"><b>{{this.wishMessage}}, Venkat!</b></div>
            </div>
            <div v-else-if="this.wishMessage=='Good Afternoon'" class="wishMessage">
                <img src="@/assets/Goodafternoon.jpeg" alt="goodafternoon" class="wishImage">
                <div class="heading"><b>{{this.wishMessage}}, Venkat!</b></div>
            </div>
            <div v-else class="wishMessage">
                <img src="@/assets/Goodevening.jpeg" alt="goodafternoon" class="wishImage">
                <div class="heading"><b>{{this.wishMessage}}, Venkat!</b></div>
            </div>
        </div>
        <div class="error" v-show="showError">
            <img src="@/assets/error.jpeg" alt="error">
        </div>
        <div class="hourlyWeather" v-show="!showError">
            <div class="hour" v-for="weather in getNextSixHours" :key="weather">
                <div>{{weather.time}}:00</div>
                <div class="weatherImages"><img :src="weather.weatherImage"></div>
                <div><b>{{weather.temprature}}</b>&#8451;</div>
                <div>{{weather.weatherStatus}}</div>
                <div>Humididty:{{weather.humidity}}</div>
                <div>Pressure: {{weather.pressure}}</div>
            </div>
        </div>
        <div class="weeklyWeather" v-show="!showError">
            <div class="hour" v-for="weather in getNextSixDays" :key="weather" @click="getDataForDate(weather.date,weather.city)">
                <div class="dates">{{weather.date}}</div>
                <div class="weatherImages"><img :src="weather.weatherImage"></div>
                <div><b>{{weather.avgTemprature}}</b>&#8451;</div>
                <div>{{weather.weatherStatus}}</div>
                <div>Min temp: {{weather.minTemperature}}&#8451;</div>
                <div>Max temp: {{weather.maxTemperature}}&#8451;</div>
                <div>pressure: {{weather.avgPressure}}</div>
                <div>Humidity: {{weather.avgHumidity}}</div>

            </div>
        </div>
        <div v-show="showDetailWeather" class="detailWeather">
            <div><img src="@/assets/close.png" class="wishImage" @click="disappear()"></div>
            <div class="hours" v-for="weather in getDetailData" :key="weather">
                <div class="dates">{{weather.date}}</div>
                <div class="weatherImages"><img :src="weather.weatherImage"></div>
                <div>{{weather.temprature}}&#8451;</div>
                <div>{{weather.time}}:00</div>
                <div>{{weather.weatherStatus}}</div>
                <!-- <div>{{weather.time}}:00</div> -->
                <!-- <div>Humididty:{{weather.humidity}}</div>
                <div>Pressure: {{weather.pressure}}</div> -->
            </div>
        </div>

    </div>
    <div class="right">
        <div class="search">
            <img class="search-btn" src="@/assets/search.png" autocomplete="off" @click="calling()">
            <input type="text" class="search-bar" placeholder="Enter the city" v-model="city" @keyup.enter="calling()">
        </div>
        <div  class="current">
            <div>{{getCurrentData.city}}</div>
            <div class="date"><b>Today, {{this.day}}</b></div>
            <div class="weatherImage"><img :src="getCurrentData.weatherImage"></div>
            <div class="temperature"><b>{{getCurrentData.temprature}}&#8451;</b></div>
            <div>{{getCurrentData.weatherStatus}}</div>
            <div class="wind">
                <img src="@/assets/wind.png" height="30px" width="30px">
                <div class="waste"> wind | {{getCurrentData.pressure}} km/h</div>
            </div>
            <div class="wind">
                <img src="@/assets/humidity.png" height="30px" width="30px">
                <div class="waste"> Hum | {{getCurrentData.humidity}} %</div>
            </div>
        </div>
          
        
        <!-- <div class="other">
            <div class="other_city">
                <div class="wind">
                    <img src="@/assets/wind.png" height="30px" width="30px">
                    <div class="waste"> wind | 17km/h</div>
                </div>
                <div class="wind">
                    <img src="@/assets/humidity.png" height="30px" width="30px">
                    <div class="waste"> Hum | 25%</div>
                </div>
            </div>
            <div class="othercity_name">
                <div><b>GNT</b></div>
                <div><b>32&#8451;</b></div>
            </div>
        </div> -->
    </div>
    <div>
        <button class="btn" @click="next()">Add Weather</button>
    </div>
</div>
</template>

<script>
import {
    mapActions,
    mapGetters
} from 'vuex';
export default {
    name: "WeatherApp",
    data() {
        return {
            time: "",
            date: "",
            wishMessage: "",
            // getWeatherImage:"",
            // hourlyWeather: [],
            // weeklyWeather: [],
            // weeklyTemperature: [],
            city: "",
            // currentWeather: [],
            day: "",
            CalenderDate: "",
            showDetailWeather: false,
            // DataForDate: [],
            // weatherStatus: "",
            showError: false,
        }
    },
    computed: {
        ...mapGetters(['getCurrentData', 'getNextSixHours', 'getNextSixDays', 'getDetailData','getWeatherStatus'])
    },
    created() {
        var today = new Date();
        const monthNames = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
        var days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
        this.date = days[today.getDay()] + ',' + today.getDate() + ' ' + monthNames[today.getMonth()] + ',' + today.getFullYear();
        this.day = today.getDate() + " " + monthNames[today.getMonth()]
        this.time = today.getHours() + ":" + today.getMinutes();
        this.CalenderDate = today.getFullYear() + "-" + String(today.getMonth() + 1).padStart(2, '0') + "-" + String(today.getDate()).padStart(2, '0');
        console.log(today.getHours())
        if (today.getHours() > 1 && today.getHours() < 12) {
            this.wishMessage = "Good morning"
        } else if (today.getHours() >= 12 && today.getHours() < 17) {
            this.wishMessage = "Good Afternoon"
        } else {
            this.wishMessage = "Good Evening"
        }

    },
    methods: {

        ...mapActions(['currentData', 'dayData', 'weekData', 'nextDetailData']),
        calling(){
            this.getCurrentReport();
this.getimage()
        },
        async getCurrentReport() {
            if (this.city == "") {
                this.showError = true
            } else {
                // await this.axios
                //     .get(`http://localhost:8083/getCurrentData/${this.city.toLowerCase()}`)
                //     .then((resp) => {
                //         this.currentWeather = resp.data.data;
                //         console.log(resp.data)
                //         if (resp.data.data.city == "N/A") {
                //             this.showError = true;
                //         } else {
                //             this.showError = false;
                //             // this.getWeatherImage(this.currentWeather.weatherStatus)
                //             this.getHourlyReport();
                //             this.getWeeklyData();
                //         }
                //     });
                this.currentData(this.city)
              
                this.getHourlyReport()
                this.getWeeklyData()
                }
                // if(getCurrentData.weatherStatus=="sunny"){

                // }

              
            
        },
        async getHourlyReport() {
            // await this.axios
            //     .get(`http://localhost:8083/getDayData/${this.city.toLowerCase()}/${this.CalenderDate}`)
            //     .then((resp) => {
            //         this.hourlyWeather = resp.data.data;
            //         console.log(resp.data)
            //     });
            const url = this.city.toLowerCase() + "/" + this.CalenderDate
            this.dayData(url)

        },
        async getWeeklyData() {
            console.log(this.CalenderDate)
            // await this.axios
            //     .get(`http://localhost:8083/getWeekData/${this.city.toLowerCase()}/${this.CalenderDate}`)
            //     .then((resp) => {
            //         this.weeklyWeather = resp.data.data;
            //         console.log(resp.data)
            //     });
            const url = this.city.toLowerCase() + "/" + this.CalenderDate
            this.weekData(url)
        },
        getDataForDate(date, city) {
            this.showDetailWeather = true;
            // this.axios
            //     .get(`http://localhost:8083/getDayData/${city.toLowerCase()}/${date}`)
            //     .then((resp) => {
            //         this.DataForDate = resp.data.data;
            //         console.log(resp.data)
            //     });
            const detailUrl = city.toLowerCase() + "/" + date;
            console.log("detail", detailUrl)
            this.nextDetailData(detailUrl)
        },
        disappear() {
            this.showDetailWeather = false;
        },
        next(){
            this.$router.push('/AddData')
        }
    }
}
</script>

<style scoped>
/* background-image: url(@/assets/background.jpeg); */
.main {
    /* background-image: url(@/assets/background.jpeg); */
    display: flex;
    flex-direction: row;
    justify-content: center;
    /* margin-top: 100px; */
    padding: 10px;
    background-repeat: no-repeat;
    background-size: 100%;
    height: 785px;
}
.btn{
    width:100px;
    height:50px;
}
.left {
    display: flex;
    flex-direction: column;
    /* background: lightskyblue; */
    /* background: #F0F5FF; */
    /* background: linear-gradient(to right, lightskyblue , white); */
    padding: 20px;
    gap: 30px;
    /* flex-wrap: wrap; */
    margin-top: 50px;
    height: 410px;
    overflow: scroll;
    /* width: 500px; */
    overflow-y: scroll;
}

.wishMessage {
    display: flex;
    align-items: center;
}

.wishImage {
    height: 40px;
    width: 40px;
    border-radius: 50%;
}

.Intro {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
}

.heading {
    color: #5c92ff;
    font-size: 20px;
}

.date {
    color: #363E64;
    font-size: 12px;
}

.dates {
    color: #363E64;
    font-size: 11px;
}

.search {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-left: 10px;
    background-color: white;
    /* box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1), 0 8px 16px rgb(0, 0, 0, 0.1); */
    border-radius: 24px;
    padding: 0.4em 1em;

}

button {
    margin: 0.5em;
    border-radius: 50%;
    border: none;
    height: 35x;
    width: 35px;
    outline: none;
    /* background: #7c7c7c2b; */
    /* background: rgb(255, 251, 122, 100); */
    /* color: white; */
    cursor: pointer;
    transition: 0.2s ease-in-out;
}

/* 
button:hover {
    background: #7c7c7c6b;
} */

.search-bar {
    border: none;
    outline: none;
    padding: 0.4em 1em;
    /* background: #7c7c7c2b; */
    color: black;
    font-family: inherit;
    font-size: 105%;
    height: 10px;
    width: 180px;
    border-radius: 24px;

}

/* .search {
    display: flex;
    align-items: center;
    margin-left: 10px;
} */

::placeholder {
    color: black;
}

.hourlyWeather {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    gap: 10px;
}

.hour {
    background: white;
    padding: 10px;
    border-radius: 10px;
    overflow: hidden;
    height: 87px;
    width: 60px;
    transition: 2s;
}

.hour:hover {
    /* background:linear-gradient(to right ,white, #5c92ff); */
    background: #9CBDFF;
    cursor: pointer;
    color: white;
    height: 200px;
    width: 150px;
}

.hours {
    background: white;
    padding: 10px;
    border-radius: 10px;
    overflow: hidden;
    height: 100px;
    width: 60px;
    transition: 2s;
}

.wind {
    display: flex;
    flex: row;
    flex-wrap: wrap;
    align-items: center;
}

.current {
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    align-items: center;
    background: #5c92ff;
    border-radius: 10px;
    color: white;
    margin: 10px;
    padding: 10px;
    gap: 10px;
}

.right {
    display: flex;
    flex-direction: column;
    /* align-items: center; */
    margin-left: 10px;
    /* background: white; */
    margin-top: 50px;
}

.temperature {
    font-size: 40px;
    color: white;
}

.other {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-between;
    margin: 10px;
    padding: 10px;
    background: #FD9AC0;
    border-radius: 10px;
    color: white;

}

.other-city .othercity_name {
    display: flex;
    flex-direction: column;
    gap: 10px;
    color: white;
}

.waste {
    margin-left: 5px;
}

.weeklyWeather {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    gap: 10px;
}

.detailWeather {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    gap: 10px;
    /* background: #FD9AC0; */
    background: #333232;
    opacity: 0.8; 
    padding: 20px;
}

.weatherImage img {
    height: 70px;
    width: 70px;
}

.weatherImages img {
    height: 50px;
    width: 50px;
}
</style>
