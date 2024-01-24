<template>
	<div 
    v-if="pokemon"
    class="pokemon-info about" 
    :class="pokemon.types && pokemon.types.length > 0 ? pokemon.types[0].type.name : ''"
  >
    <RouterLink to="/">
      <span>
        &#10094 Volver al listado
      </span>
    </RouterLink>

    <h1 v-if="error">{{ error }}</h1>

    <img v-if="pokemon.sprites" :src="pokemon.sprites.other.home.front_default" alt="pokemonImage" class="pokemon-image">
    <div class="infoBox">
      <div>
        <h1 v-if="pokemon.name">{{ pokemon.name.toUpperCase() }}</h1>
        <div v-if="pokemon.description">
          <h3>Descripción</h3> 
          <p>{{ pokemon.description }}</p>
        </div>
        <div>
          <h3>Detalles</h3>
          <p>Peso: {{ pokemon.height }} KG</p>
          <p>
            Tipo:
            <strong v-for="oneType in pokemon.types">
              {{oneType.type.name}} |
            </strong>
          </p>          
        </div>
      </div>
      <h2 v-if="pokemon.moves">Lista de movimientos</h2>
      <div v-if="pokemon.moves" class="buttonsBox">
        <span 
          v-if="start != 0" 
          :class="pokemon.types && pokemon.types.length > 0 ? pokemon.types[0].type.name : ''" 
          @click="start -= 10, end -= 10"
        >
          &#10094 Anterior
        </span>
        <span
          v-if="pokemon.moves && pokemon.moves.slice(this.start, this.end).length == 10"
          :class="pokemon.types && pokemon.types.length > 0 ? pokemon.types[0].type.name : ''" 
          @click="start += 10, end += 10"
        >
          Siguiente &#10095
        </span>
      </div>
      <div class="movesBox" v-if="pokemon.moves">
        <span 
          v-for="(onePokeMove, idxMove) in pokemon.moves.slice(this.start, this.end)" 
          class="oneMove" 
          :style="getMoveStyle(idxMove)"
        >
          {{ onePokeMove.move.name }}
        </span>
      </div>  
    </div>
	</div>
</template>
  
<script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        pokemon: [],
        start: 0,
        end: 10,
        error: null
      }
    },
  
    methods: {

      getMoveStyle(idxMove) {
        const randomColorNumber = (idxMove * 0x5f6d2e) & 0xffffff;

        // Convierte el número en una cadena hexadecimal de 6 dígitos
        const randomColorHex = randomColorNumber.toString(16).padStart(6, '0');

        // Agrega el símbolo "#" al principio para obtener el formato completo del color
        const randomColor = "#" + randomColorHex;

        // Función para calcular la luminancia de un color
        function getLuminance(hexColor) {
          const hex = hexColor.replace(/^#/, "");
          const r = parseInt(hex.slice(0, 2), 16);
          const g = parseInt(hex.slice(2, 4), 16);
          const b = parseInt(hex.slice(4, 6), 16);
          return (0.299 * r + 0.587 * g + 0.114 * b) / 255;
        }

        // Umbral para decidir si el texto debe ser blanco o negro
        const luminanceThreshold = 0.7; // Ajusta este valor según tus preferencias

        // Determina el color de texto según la luminancia
        const textColor = getLuminance(randomColor) < luminanceThreshold ? "#fff" : "#000";

        return { backgroundColor: randomColor, color: textColor };
      }
    },
  
    async mounted() {

      if (this.$route.params.pokemonId) {

        this.error = null

        await axios
        .get('https://pokeapi.co/api/v2/pokemon/' + this.$route.params.pokemonId)
        .then(response => {
          this.pokemon = response.data
        })
        .catch(error => {
          console.error('Error al cargar Pokémon:', error);
          this.error = error.message || 'Ha ocurrido un error';
        }) 


        await axios
        .get('https://pokeapi.co/api/v2/characteristic/'+ this.$route.params.pokemonId)
        .then(response => {
  
          const characteristic = response.data
          
          const spanishDescription = characteristic.descriptions.find(
            description => description.language.name === 'es'
          ).description
  
          this.pokemon.description = spanishDescription
  
        })
        .catch(error => {
          console.error('Error al cargar Pokémon:', error);
        })   
      }
    }
  }

</script>
  
<style scoped>

.buttonsBox {
  display: flex;
  justify-content: space-around;
  margin: 1rem;
}

a {
  text-decoration: none;
  color: #000;
}

span {
  cursor: pointer;
  padding: 0 5px;
  border-radius: 10px;
  transition: all 0.2s ease-in-out;
  user-select: none;
  font-weight: 600;
  display: flex;
}

span:hover {
  transform: translateY(-3px);
}

.oneMove {
  display: inline-block;
  padding: 5px 10px;
  border-radius: 15px;
  margin: 5px;
}

.movesBox {
  display: flex;
  flex-wrap: wrap;
  background: #e7e7e7;
  border-radius: 5px;
  justify-content: space-evenly;
}

.infoBox {
  background: white;
  padding: 5px;
  border-radius: 10px;
}

.pokemon-info {
  text-align: center;
  width: 25rem;
  margin: 0 auto;
  padding: 5px;
  border-radius: 5px;
  border: 1px solid #ccc;
  background: #b77575;
}
.pokemon-image {
  border-radius: 10px;
  width: 100%;
  border-radius: 10px;
  background-image: radial-gradient(circle at 50% 50%, #ffffff, transparent);
}

.pokemon-details {
  margin-top: 20px;
}

h2 {
  color: #333;
  font-weight: 600;
}

h1 {
  font-size: 24px;
  font-weight: bold;
  text-align: center;
  color: #000;
}

p {
  color: #666;
  font-family: 'Open Sans', sans-serif;
}

ul {
  list-style-type: none;
}

li {
  margin-bottom: 10px;
}

h3 {
  margin-bottom: 5px;
}

</style>
  