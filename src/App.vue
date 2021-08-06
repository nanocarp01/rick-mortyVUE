<template>
  <div id="app"> 
    <div class="hero is-white is-gradient is-bold">

      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-success">R-and-M</span>
          <span class="subtitle">Personajes</span>
        </h1>

        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input 
            v-model="search" 
            class="input is-rounded" 
            type="text" 
            placeholder="Buscar..." 
            v-on:keyup.enter="searchData">
          </div>
          <div class="control">
            <button class="button is-primary is-rounded"  v-on:click="searchData" >Buscar</button>
          </div>
        </div>

      
      </div>

    </div>

    <div class="container">
      <div class="columns is-desktop is-mobile is-tablet is-multiline is-centered" >
        
        <character
        @showModal="showModal" 
        v-for="character of characters" 
        v-bind:key="character.id" 
        v-bind:character="character"/>
      </div>

      <nav class="pagination" role="navegation" aria-label="pagination">
        <a class="pagination-previous" v-on:click="changePage( page - 1 );">Anterior</a>

        <ul class="pagination-list">
          <li>
            <a class="pagination-link is-current">{{ page }}</a>
          </li>
        </ul>

        <a class="pagination-next" v-on:click="changePage(page + 1);">Siguiente</a>
      </nav>
    </div>
     <div class="modal" :class="{'is-active': modal }" v-if="modal">
        <div class="modal-background" @click="modal = false" ></div>
        <div class="modal-card">
          <header class="modal-card-head">
            <p class="modal-card-title">Acerca de: {{ currentCharacter.name }}</p>
          </header>

          <div class="modal-card-body">
            <p>Genero: <strong>{{currentCharacter.gender}}</strong> </p>
            <p>Estado: <strong>{{currentCharacter.status}}</strong> </p>
            <p>Raza: <strong>{{currentCharacter.species}}</strong></p>
            <p>Tipo: <strong>{{currentCharacter.type}}</strong></p>
          </div>
         
          <footer class="modal-card-foot">
           
              <button class="button is-success " @click="modal = false">Cerrar</button>
      
            
          </footer>  
        </div>/
     </div>



  </div>
</template>

<script>
import axios from 'axios';

import Character from './components/Character';

export default {
  name: 'App',
  components:{
    Character
  },
  data: function() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: '',
      modal:false,
      currentCharacter: {

      }
    };
  },

  created() {
    this.fetch();
  },
  methods:{
    fetch() {
      const params = {
        page: this.page,
        name: this.search,
      }

      let result = axios
      .get("https://rickandmortyapi.com/api/character/", { params  })
      .then(res => {
        this.characters = res.data.results;
        this.pages = res.data.info.pages;
        //console.log(res.data);
      })
      .catch(err => {
        console.log(err);
      });
    
    },
    changePage(page) {
      this.page = (page <= 0 || page > this.pages) ? this.page: page;
      this.fetch();
    },
    searchData() {
      this.page = 1;
      this.fetch();
    },
    showModal(id) {
      this.fetchOne(id);
    },
    async fetchOne(id) {
      //llamada http
      let result = await axios.get(`https://rickandmortyapi.com/api/character/${id}/`)
      this.currentCharacter = result.data;
      this.modal = true;

      console.log(this.currentCharacter, 'Personaje');

    }
  }
};
</script>

<style lang="scss">

</style>
