<template>
    <div v-if="ciudadSeleccionada" class="container mt-5">
        <router-link to="/" class="btn btn-outline-primary mb-4">
            <i class="bi bi-arrow-left"></i> Volver a las ciudades
        </router-link>

        <div class="row shadow-lg p-4 rounded bg-white">
            <div class="col-md-5">
                <img :src="ciudadSeleccionada.img" class="img-fluid rounded mb-3" :alt="ciudadSeleccionada.nombre">  
            </div>

            <div class="col-md-7">
                <h2 class="display-5 fw-bold">{{  ciudadSeleccionada.nombre }}</h2>
                <hr>

                <div v-if="clima" class="my-4">
                    <h4 class="text-secondary">Estado del clima:</h4>
                    <p class="fs-4">
                        🌡️ Temperatura: 
                        <strong>{{ convertirTemp(clima?.current_weather?.temperature) }}{{ unidad }}</strong>

                        <button @click="esCelsius = !esCelsius" class="btn btn-sm btn-outline-secondary ms-3">
                            Ver en {{  esCelsius ? 'Fahrenheit' : 'Celsius'}}
                        </button>
                    </p> 

                    <p class="fs-5 text-muted">
                        Viento: {{  clima.current_weather?.windspeed }} km/h
                    </p>          
                </div>

                <div v-if="estadisticas" class="mt-4 bg-light p-3 rounded">
                    <h5 class="mb-3">Estadísticas de la semana</h5>
                    <p><strong>Promedio diario:</strong> {{  convertirTemp(Number(estadisticas.avg)) }}{{ unidad }}</p>
                    <p><strong>Máxima:</strong> {{ convertirTemp(Number(estadisticas.max)) }}{{  unidad }}</p>
                    <p><strong>Mínima:</strong> {{ convertirTemp(Number(estadisticas.min)) }}{{ unidad }}</p>
                    <p><strong>Días con lluvia:</strong>  {{ estadisticas.diasLluvia }} días</p>
                    <p><strong>Días soleados:</strong>  {{ estadisticas.diasSoleados }} días</p>
                </div>

                <div v-else class="spinner-border text-primary" role="status">
                    <span class="visually-hidden">Cargando...</span>
                </div>

                <div v-if="clima && clima.daily" class="my-5">
                    <h4 class="text-secondary">Pronóstico de la semana</h4>
                    <div class="row row-cols-2 row-cols-md-5 g-2">
                        <div 
                            v-for="(day, index) in clima.daily.time.slice(0, 7)"
                            :key="index"
                            class="col text-center border rounded p-2"
                    >
                            <small class="fw-bold text-dark">
                                {{ obtenerDia(day) }}
                            </small>

                            <p class="mb-1">🌡️{{  clima.daily.temperature_2m_min[index] }}°</p>

                            <span v-if="clima.daily.precipitation_sum[index] > 0" class="badge bg-primary">
                            LLuvia
                            </span>

                            <span v-else class="badge bg-success">
                            Soleado
                            </span>
                        </div>
                    </div>
                </div>

                <div class="alert alert-info mt-4 border-0 shadow-sm">
                    <h5 class="fw-bold"> 💡Consejo de bienestar:</h5>
                    <p class="texto-justificado mb-0 fs-5 italic">{{  ciudadSeleccionada.mensaje }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { ciudades} from '@/data/ciudades.js'

const props =defineProps(['id'])

const ciudadSeleccionada = ref(null)

const esCelsius = ref(true) 

const convertirTemp = (temp) => {
    if (temp === undefined || temp === null) return "0.0"
    const valor =  esCelsius.value ? temp :  (temp * 9/5) + 32
    return Number(valor).toFixed(1)
}

const unidad = computed(() => esCelsius.value ? '°C' : '°F')

const clima = ref(null)



const estadisticas  = computed (() => {
    if (!clima.value || !clima.value.daily) return null

    const d = clima.value.daily

   
    const tempsMax = d.temperature_2m_max || []
    const tempsMin = d.temperature_2m_min || []
    const precipitation = d.precipitation_sum || []

    if (tempsMax.length === 0) return null

    
    const max = Math.max(...tempsMax)
    const min = Math.min(...tempsMin)

    const suma = tempsMax.reduce((a, b) => a + b, 0)

    const avg = suma  / tempsMax.length

    const diasLluvia = precipitation.filter(h => h > 0).length;
    const diasSoleados = precipitation.length - diasLluvia;

    return {
        max: max,
        min: min,
        avg:avg,
        diasLluvia,
        diasSoleados
    };
});

const obtenerClima = async (lat, lon) => {
    try {
        const url=`https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true&daily=temperature_2m_max,temperature_2m_min,precipitation_sum&timezone=auto`;
        const response = await fetch(url)
        if (!response.ok) throw new Error("Error en la respuesta de la API");
        
        const data = await response.json()
        clima.value = data
    } catch (error) {
        console.error("Error al obtener el clima:", error)
    }
}

const obtenerDia = (fecha) => {
    const  dias = ["Domingo", "Lunes", "Martes", "Miércoles", "Jueves", "Viernes", "Sábado"]
    const date = new Date(fecha + 'T00:00:00')
    return dias[date.getDay()]
}


onMounted(() => {

    const idBusqueda = Number(props.id)
    const idNumber = Number(props.id)
    ciudadSeleccionada.value = ciudades.find(c => c.id === idBusqueda)

    if (ciudadSeleccionada.value) {
        obtenerClima(
            ciudadSeleccionada.value.lat, 
            ciudadSeleccionada.value.lon
        )
    } else {
        console.error("No se encontró la ciudad con ID:", idBusqueda)
    }
})
   
</script>