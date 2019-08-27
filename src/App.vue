<template>
    <div id="app">
        <vue-vapor-upload-component
            accept=".csv"
            endpoint="/backstage/players"
            name="players-input"
            :reload-after-upload="true"
        ></vue-vapor-upload-component>
    </div>
</template>

<script>
// import Vapor from 'laravel-vapor';
window.Vapor = require('laravel-vapor');
const axios = require('axios');
window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';

import VueVaporUploadComponent from './components/VueVaporUploadComponent.vue'

export default {
    name: 'app',
    components: {
        VueVaporUploadComponent
    }
}
</script>

<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
