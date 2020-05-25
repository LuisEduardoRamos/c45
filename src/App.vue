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
        <b-row style="margin-top:10px;">
          <b-col>
            <b-alert show variant="danger">No igresar datasets con columna de indexado.</b-alert>
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
            <b-button @click="parsearCsv" variant="outline-primary">Parsear CSV</b-button>
          </b-col>
        </b-row>
        <b-row>
          <b-col cols="3">
            <div class="mt-3 black">Archivo seleccionado: </div>
            <div class="mt-3">{{ file ? file.name : '' }}</div>
          </b-col>
          <b-col cols="3">
            <div class="mt-3">
              <div class="mt-3 black">Clase de decisión: </div>
              <b-form-select v-model="decision" :options="options"></b-form-select>
            </div>
          </b-col>
          <b-col cols="3">
            <div class="mt-3">
              <div class="mt-3 transparent">h</div>
              <b-button @click="c45" variant="success">Calcular C4.5</b-button>
            </div>
          </b-col>
        </b-row>
        <b-row style="margin-top:10px;" :key="item.iteracion" v-for="item in response">
          <b-col>
            <b-list-group >
              <b-list-group-item variant="dark">Iteración: {{item.iteracion}}</b-list-group-item>
              <b-list-group-item>Relación: {{item.relacion || '-'}}</b-list-group-item>
              <b-list-group-item>Clase ganadora: {{item.claseGanadora|| '-'}}</b-list-group-item>
              <b-list-group-item>Entropía global: {{item.entropiaGlobal }}</b-list-group-item>
              <b-list-group-item class="black">Hijos clase ganadora:</b-list-group-item>
              <b-list-group-item variant="success" v-for="x in item.hijosGanadora">{{x}}</b-list-group-item>
              <b-list-group-item class="black">Entropías:</b-list-group-item>
              <b-list-group-item variant="success" v-for="y in item.entropias">{{`Entripía de la clase ${y.clase}: ${y.entropia} `}}</b-list-group-item>
              <b-list-group-item variant="info">Desicion final: {{item.desicionFinal|| '-'}}</b-list-group-item>
            </b-list-group>
          </b-col>
        </b-row>
    </b-container>
  </div>
</template>

<script>

import axios from 'axios';
import c45 from './util/c45';

export default {
  name: 'App',
  data() {
    return {
      file:null,
      responseCsv:null,
      decision: null,
      options:[],
      responseC45:[],
      response:[]
    }
  },
  methods:{
    async parsearCsv(){
      const formData = new FormData();
		  formData.append('csv', this.file);
      this.responseCsv = await axios.post('http://192.168.2.10:3000/api/upload', formData , {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      });
      console.log(this.responseCsv.data.clases)
      this.options = this.responseCsv.data.clases.map((clase)=>({ value:clase,text:clase }));
    },
    async c45(){
      this.response = [];
      this.response = await c45(this.responseCsv.data.datos, this.decision);
      console.log(this.response)
    },

  }
}
</script>

<style>
  .black{
    font-weight: bold;
  }
  .transparent{
    color:white;
  }
  #rowInput{
    margin-top: .5em;
  }
</style>
