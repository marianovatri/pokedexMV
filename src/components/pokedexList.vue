<template>
  <div class="about">
    <div class="buttonsBox">
      <button 
        v-if="pokedexInfo.previous" 
        @click="loadPokemonList(pokedexInfo.previous)" 
        class="pokedex-button"
      >
       &#10094 Anterior
      </button>

      <button 
        v-if="pokedexInfo.next" 
        @click="loadPokemonList(pokedexInfo.next)" 
        class="pokedex-button"
      >
        Siguiente &#10095
      </button>
    </div>

    <div class="pokemonBox" v-if="pokedexInfo.results && !error">
      <div class="pokemonCard"
        v-for="onePokemon in pokedexInfo.results"
        :key="onePokemon.id"
        @click="showMeThePokemon(onePokemon)"
        :class="onePokemon.types && onePokemon.types.length > 0 ? onePokemon.types[0].type.name : ''"
      >
        <div>
          <img :src="onePokemon.image" alt="">
          <h2>{{ onePokemon.name.toUpperCase() }}</h2>

          <div>
            <span>Tipo: </span>
            <strong v-for="oneType in onePokemon.types">
              {{oneType.type.name}} |
            </strong>
          </div>

          <span>Peso: {{ onePokemon.weight }} KG</span>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      pokedexInfo: [],
      error: null,
    }
  },

  methods: {

    /** carga el listado de pokemons */
    async loadPokemonList(urlReceived) {

      this.pokedexInfo = []

      const getUrl = urlReceived || 'https://pokeapi.co/api/v2/pokemon';

      await axios
      .get(getUrl)
      .then(response => {
        this.pokedexInfo = response.data

         this.pokedexInfo.results.forEach(onePokemon => {
          axios 
          .get(onePokemon.url)
          .then(response => {
            const pokemonInfo = response.data

            onePokemon.id = pokemonInfo.id
            onePokemon.weight = pokemonInfo.weight
            onePokemon.types = pokemonInfo.types
            onePokemon.image = pokemonInfo.sprites.other.home.front_default
          })
          .catch(error => {
            console.error('Error al cargar la lista de Pokémon:', error);
            this.error = error.message || 'Ha ocurrido un error';
          })
        });
      })
      .catch(error => {
        console.error('Error al cargar la lista de Pokémon:', error);
        this.error = error.message || 'Ha ocurrido un error';
      })      
    },

    /** va a la pantalla de informacion sobre el pokemon seleccionado */
    showMeThePokemon(onePokemon) {
      this.$router.push({ name: 'pokemonInfo', params: {pokemonId: onePokemon.id }}) 
    },
  },

  mounted() {
    this.loadPokemonList()
  }
}
</script>

<style scoped>

.buttonsBox {
  display: flex;
  justify-content: center;
}

.pokedex-button {
  width: 100px;
  height: 30px;
  border-radius: 10px;
  border: 0;
  margin: 1rem;
  background-color: #877aff;
  color: #ffffff;
  inline-size: auto;
  font-size: 18px;
  box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.2);
  font-family: 'PixelMplus', sans-serif;
  cursor: pointer;
}

.pokedex-button:hover {
  background-color: #6f61f1;
}

.pokemonBox {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}
.pokemonCard {
  background-color: #fff; 
  cursor: pointer;
  text-align: center;
  color: black;
  border-radius: 10px;
  margin: 20px;
  padding: 15px;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.25);
  transition: all 0.2s ease-in-out;
}

.pokemonCard img {
  width: 150px;
  height: 150px;
  background-image: radial-gradient(circle at 50% 50%, #ffffff, transparent);
  border-radius: 50%;
  margin-bottom: 10px;
  display: block;
}

.pokemonCard:hover {
  box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.2);
  transform: translateY(-8px);
}

.pokemonCard ul {
  list-style: none;
  padding: 0;
  text-align: center;
}

.pokemonCard li {
  margin-bottom: 5px;
}

.pokemonCard li:first-of-type {
  font-weight: bold;
}

</style>
