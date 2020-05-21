<template>
  <div id="app">
    <b-container fluid>
        <b-row>
          <b-col>
            <b-navbar toggleable type="dark" variant="dark">
              <b-navbar-brand href="https://github.com/LuisEduardoRamos">Luis Eduardo Ramos Granados 15171256</b-navbar-brand>
            </b-navbar>            
          </b-col>
        </b-row>
        <b-row id="rowInput" cols="8">
          <b-col>
            <b-form-file
              v-model="file"
              :state="Boolean(file)"
              placeholder="Eliga o arrastre un archivo .csv"
              drop-placeholder="Arrastre el archivo aquí..."
              accept=".csv"
            ></b-form-file>
          </b-col>
          <b-col cols="2">
            <b-button @click="parsearCsv" variant="outline-primary">Calcular</b-button>
          </b-col>
        </b-row>
        <b-row>
          <b-col cols="3">
            <div class="mt-3 black">Archivo seleccionado: </div>
          </b-col>
          <b-col>
            <div class="mt-3">{{ file ? file.name : '' }}</div>
          </b-col>
        </b-row>
    </b-container>
  </div>
</template>

<script>

import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      file:null
    }
  },
  methods:{
    parsearCsv(){
      let formData = new FormData();
      formData.append('file', this.file);
      axios.post('192.168.2.10:3000/api/upload', 
      formData, formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }).then(function(response){
        console.log(respose);
      }).catch(function(err){
        console.log(err);
      })
    }
  }
}
</script>

<style>
  .black{
    font-weight: bold;
  }
  #rowInput{
    margin-top: .5em;
  }
</style>
