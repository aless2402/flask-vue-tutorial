<template>
    <div>
        <button type="button" class="btn btn-primary">{{ msg }}</button>
        <div v-if="dataLoaded">
            <p>Data received: {{ responseData }}</p>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'Shark',
    data() {
        return {
            msg: "Hello, this is Shark 🦈",
            responseData: null,
            dataLoaded: false
        };
    },
    methods: {
        getresponse() {
            const path = 'http://127.0.0.1:5000/shark'; // Reemplaza 'URL_DE_TU_API' con la URL de tu API
            axios.get(path)
                .then(response => {
                    this.responseData = response.data;
                    this.dataLoaded = true;
                })
                .catch(error => {
                    console.error('Error fetching data:', error);
                });
        }
    },
    created() {
        this.getresponse(); // Llamamos al método getresponse cuando el componente se crea
    }
}
</script>
