<template>
  <header class="header">
    <label for="pokemonInput">Type pokemon name or ID:</label>
    <input type="text" id="pokemonInput" v-model="pokemonID" required>
    <button class="searchButton" @click="searchPokemon">Buscar Pokemon</button>
  </header>
  <main class="main" :class="pokemonTypeClass" v-if="Object.entries(pokemonData).length > 0">
    <section class="pokemonCard">
      <div class="nameImage">
        <h1 class="pokemonName hidden" ref="pokemonName">{{ pokemonData.name }}</h1>
        <div class="imageContainer hidden" ref="pokemonImages">
          <img :src="pokemonData.sprites.front_default">
          <img :src="pokemonData.sprites.back_default">
        </div>
      </div>
      <ul class="type hidden" ref="pokemonTypes">
        <h2>Tipo Pokemon</h2>
        <li v-for="(type, key) in pokemonData.types" :key="key" :class="type.type.name">
          <span>{{ type.type.name }}</span>
        </li>
      </ul>
      <!-- <div class="abilityDescription" ref="effect_entries.effect">
        <h2>Descripción de la Habilidades</h2>
        <p>{{ abilityDescription }}</p>
      </div> -->
      <ul class="stats hidden" ref="pokemonStats">
        <h2 class="h2">Estadisticas</h2>
        <li v-for="(stat, key) in pokemonData.stats" :key="key">
          <span>{{ stat.stat.name}} = {{stat.base_stat }}</span>
        </li>
      </ul>
    </section>
  </main>
</template>

<script>
import { pokeapi } from '@/api/pokeapi'

export default {
  name: 'App',
  data() {
    return {
      pokemonData: {},
      pokemonID: '',
      abilityDescription: '',
    }
  },
  methods: {
    async searchPokemon() {
      try {
        let url;
        if (!isNaN(this.pokemonID)) {
          url = `${pokeapi}/${this.pokemonID}`;
        } else {
          url = `${pokeapi}/${this.pokemonID.toLowerCase()}`;
        }
        const pokemonToFind = await fetch(url);
        const pokemon = await pokemonToFind.json();
        
        if (pokemon.name) {
          this.pokemonData = pokemon;
          this.resetAnimationClasses();
          this.$nextTick(() => {
            this.addAnimationClasses();
          });
        } else {
          throw new Error('El Pokémon no fue encontrado');
        }

        return pokemon;
      } catch (error) {
        alert('El Pokemon No Fue Encontrado');
      }
    },
    resetAnimationClasses() {
      const refs = [
        this.$refs.pokemonName,
        this.$refs.pokemonImages,
        this.$refs.pokemonTypes,
        this.$refs.pokemonStats
      ];
      refs.forEach(ref => {
        ref.classList.remove('animate__fadeInUp');
        ref.classList.remove('animate__delayedFadeInUp');
      });
      refs.forEach(ref => ref.classList.add('hidden'));
    },
    addAnimationClasses() {
      this.$refs.pokemonName.classList.remove('hidden');
      this.$refs.pokemonName.classList.add('animate__fadeInUp');

      setTimeout(() => {
        this.$refs.pokemonImages.classList.remove('hidden');
        this.$refs.pokemonImages.classList.add('animate__fadeInUp');
      }, 200);

      setTimeout(() => {
        this.$refs.pokemonTypes.classList.remove('hidden');
        this.$refs.pokemonTypes.classList.add('animate__delayedFadeInUp');
      }, 400);

      setTimeout(() => {
        this.$refs.pokemonStats.classList.remove('hidden');
        this.$refs.pokemonStats.classList.add('animate__delayedFadeInUp');
      }, 600);
    }
  },
  computed: {
    pokemonTypeClass() {
      if (this.pokemonData.types) {
        const pokemonType = this.pokemonData.types[0].type.name;
        return pokemonType;
      }
      return 'default';
    }
  }
}
</script>

<style lang="scss" scoped>
@import './pokemon_types.scss';
@import url('https://fonts.googleapis.com/css2?family=Changa:wght@400;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');

@keyframes fadeInUp {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate__fadeInUp {
  animation: fadeInUp 0.5s ease-in-out;
}

.animate__delayedFadeInUp {
  animation: fadeInUp 0.5s ease-in-out 0.3s;
  animation-fill-mode: backwards;
}

.hidden {
  opacity: 0;
}
.h2{
  background-color: silver;;
}


body {
  font-family: 'Changa', sans-serif;
  background-color: #f5f5f5;
  color: #333;
}


.header {
  background-color: #4CAF50;
  padding: 20px;
  text-align: center;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.header label {
  font-weight: bold;
  margin-right: 10px;
}

.header input {
  padding: 5px;
  border-radius: 5px;
  border: none;
  margin-right: 10px;
}

.searchButton {
  padding: 5px 10px;
  border-radius: 5px;
  background-color: #333;
  color: white;
  border: none;
  cursor: pointer;
}

.main {
  background-color: white;
  border-radius: 10px;
  border: 10px solid gold; 
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  padding: 20px;
  margin: 20px auto;
  max-width: 600px;
}

.pokemonCard {
  text-align: center;
}

.nameImage {
  margin-bottom: 20px;
}

.pokemonName {
  text-transform: uppercase;
  margin-bottom: 10px;
  text-align: left;
}

.type, .stats {
  list-style: none;
  padding: 0;
  margin: 10px 0;
}

.type li, .stats li {
  display: inline-block;
  margin-right: 10px;
  padding: 5px 10px;
  border-radius: 5px;
  color: white;
}

.stats {
  display: flex; 
  flex-direction: column; 
  gap: 5px;
}

.stats li {
  display: flex; 
  align-items: center; 
  background-color: gray;
  border-radius: 10px;
}

.stats li span:last-child {
  left: 10px;
}

.imageContainer {
  position: relative; 
  background-image: url('fondo_p.jpg'); 
  background-size: cover; 
  background-position: center; 
  padding: 20px; 
}

.imageContainer img {
  width: 200px;
  height: 200px; 
  margin: auto; 
}

.normal { background-color: $normal }
.fire { background-color: $fire }
.water { background-color: $water }
.grass { background-color: $grass }
.electric { background-color: $electric }
.ice { background-color: $ice }
.poison { background-color: $poison }
.ground { background-color: $ground }
.flying { background-color: $flying }
.psychic { background-color: $psychic }
.bug { background-color: $bug }
.rock { background-color: $rock }
.ghost { background-color: $ghost }
.dark { background-color: $dark }
.dragon { background-color: $dragon }
.steel { background-color: $steel }
.fairy { background-color: $fairy }

.type li span {
  font-size: 25px; 
  color: rgb(0, 0, 0); 
  background-color: gray;
  border-radius: 5px;
  padding: 5px;
  margin: 5px; 
}
</style>