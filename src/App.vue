<template>
    <div id="weather-app">
        <div class="weather" :class="hasData ? state : ''">
            <div class="container">
                <h1 class="weather__search" v-if="cityFound == null">
                    search for city
                </h1>
                <div class="input-search">
                    <input
                        type="text"
                        class="weather__input"
                        placeholder="Search..."
                        v-model="city"
                        @keydown="getWeather"
                    />
                </div>

                <div class="weather__info" v-if="hasData">
                    <h2 class="weather__city">{{ cityName }}</h2>
                    <h3 class="weather__day">{{ day }}</h3>
                    <h3 class="weather__temp">{{ temp }}c°</h3>
                    <span class="weather__state">{{ state }}</span>
                </div>
                <p class="no-city" v-if="cityFound">no city found</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "weather",
    data() {
        return {
            hasData: false,
            cityFound: null,
            cityName: "",
            temp: "",
            day: "",
            state: "",
            api_url: "http://api.openweathermap.org/data/2.5/weather",
            api_key: "24404cd0907841d2d041e7ad259cc5cb",
            city: "",
        };
    },
    methods: {
        async getWeather(e) {
            if (e.keyCode == 13) {
                let fullURL = `${this.api_url}?q=${this.city}&appid=${this.api_key}&units=metric`;
                try {
                    const getData = await fetch(fullURL);
                    const data = await getData.json();
                    this.cityName = data.name;
                    this.temp = Math.round(data.main.temp);
                    this.state = data.weather[0].main.toLowerCase();
                    this.day = this.getDay();
                    this.hasData = true;
                    this.city = "";
                    this.cityFound = false;
                } catch (e) {
                    this.city = "";
                    this.hasData = false;
                    this.cityFound = true;
                }
            }
        },
        getDay() {
            const d = new Date();
            const weekday = [
                "Sunday",
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday",
                "Saturday",
            ];

            const months = [
                "January",
                "February",
                "March",
                "April",
                "May",
                "June",
                "July",
                "August",
                "September",
                "October",
                "November",
                "December",
            ];
            let day = weekday[d.getDay()];
            let month = months[d.getMonth()];
            let dayInMonth = d.getDate();
            let year = d.getFullYear();

            return `${day} ${dayInMonth} ${month} ${year}`;
        },
    },
    mounted() {
        window.onload = document.querySelector(".weather__input").focus();
    },
};
</script>

<style lang="scss">
* {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
    font-family: "Poppins", sans-serif;
}

.container {
    width: 80vw;
    margin: 0 auto;
}

.weather {
    height: 100vh;
    padding-top: 2rem;
    background-size: cover;
    background-position: 50% 60%;
    position: relative;
    transition: 0.5s ease-in-out;
    text-align: center;
    background-color: #86bcce;
    &.clear {
        background-image: url(./assets/clear.jpg);
    }
    &.rain {
        background-image: url(./assets/rain.jpg);
    }
    &.clouds {
        background-image: url(./assets/cloud.jpg);
    }
    &.mist {
        background-image: url(./assets/mist.jpg);
    }
    &.snow {
        background-image: url(./assets/snow.jpg);
    }
    &.clear::before,
    &.rain::before,
    &.clouds::before,
    &.mist::before,
    &.snow::before {
        content: "";
        position: absolute;
        inset: 0;
        background: linear-gradient(to top, rgba(black, 0.7), transparent);
        pointer-events: none;
    }

    &__search {
        text-transform: capitalize;
        text-shadow: 3px 3px 3px rgba(black, 0.4);
    }

    &__input {
        display: block;
        margin: 1rem auto;
        width: 80%;
        font-size: 1.4rem;
        border: none;
        border-radius: 5px;
        padding: 1rem;
        background: rgba(white, 0.5);
        box-shadow: 5px 10px 15px -5px rgba(black, 0.1);
        transition: 0.5s ease;
        &:focus,
        &:hover {
            outline: none;
            background: rgba(white, 0.8);
            box-shadow: 5px 10px 15px -5px rgba(black, 0.3);
        }
    }

    &__info {
        position: relative;
        margin: 2rem auto;
        text-transform: capitalize;
        z-index: 2;
        color: white;
    }

    &__city {
        font-size: 1.5rem;
        text-shadow: 3px 3px 2px rgba(black, 0.2);
    }

    &__day {
        text-shadow: 3px 3px 2px rgba(black, 0.2);
        font-weight: normal;
    }
    &__temp {
        font-size: clamp(7rem, 6rem + 5vw, 10rem);
        text-shadow: 5px 5px 2px rgba(black, 0.4);
    }
    &__state {
        font-size: 2rem;
        text-shadow: 5px 5px 2px rgba(black, 0.4);
    }

    .no-city {
        font-size: 2rem;
        margin: 2rem auto;
        text-transform: capitalize;
        font-weight: bold;
    }
}
</style>
