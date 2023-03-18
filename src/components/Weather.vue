<template>
    <div :class="['weather-wrapper', 'weather-container', backgroundClass]">
        <div class="weather-content">
            <h2>Previsão do Tempo</h2>
            <input v-model="city" @input="fetchWeather" placeholder="Digite o nome da cidade" />
            <p v-if="temperature !== null">A temperatura atual em {{ city }} é {{ temperature }} °C</p>
            <p v-if="error">{{ error }}</p>
        </div>
    </div>
</template>

<style scoped>
    @import "@/assets/styles.css";
</style>
  
<script lang="ts">
    import { computed, defineComponent, ref } from "vue";
    import axios from "axios";

    export default defineComponent({
    setup() {
        const city = ref("");
        const temperature = ref<number | null>(null);
        const error = ref("");
        const backgroundClass = computed(() => getBackgroundClass(temperature.value));

        function getBackgroundClass(temp: number | null): string {
            if (temp === null) return "";

            const hour = new Date().getHours();
            const isDaytime = hour >= 6 && hour < 18;

            if (temp < 10) {
                return isDaytime ? "cold-day" : "cold-night";
            } else if (temp < 20) {
                return isDaytime ? "mild-day" : "mild-night";
            } else {
                return isDaytime ? "warm-day" : "warm-night";
            }
        }

        const fetchWeather = async () => {
        if (!city.value) {
            return;
        }

        const apiKey = "2dd6a285e061225f7477e25904e3f112";
        const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&appid=${apiKey}&units=metric`;

        try {
            const response = await axios.get(apiUrl);
            temperature.value = response.data.main.temp;
            error.value = "";
            
        } catch (err) {
            error.value = "Não foi possível obter a temperatura. Verifique o nome da cidade.";
            temperature.value = null;
        }
        };

        return { city, temperature, error, fetchWeather, backgroundClass };
    },
    });
</script>
  