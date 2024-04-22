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

        let url = pokeapi + "/" + this.pokemonID.toLowerCase();
        
        const pokemon = await fetch(url).then(res => res).then(data => data.json());
        
        if (pokemon.name) {
          this.pokemonData = pokemon;
          const ability = pokemon.abilities.find(a => !a.is_hidden);
          if (ability && ability.ability.effect_entries) {
            const spanishEntry = ability.ability.effect_entries.find(entry => entry.language.name === 'es');
            this.abilityDescription = spanishEntry ? spanishEntry.short_effect : ability.ability.effect_entries[0].short_effect;
          }
          this.resetAnimationClasses();
          this.$nextTick(() => {
            this.addAnimationClasses();
          });
        } 
        return pokemon;
      } catch (error) {
        alert('El Pokemon No Fue Encontrado');
      }
    },
    resetAnimationClasses() {
      const {pokemonName, pokemonImages, pokemonTypes,pokemonStats} = this.$refs;
      if (pokemonName, pokemonImages,pokemonTypes, pokemonStats) {
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
      }
    },
    addAnimationClasses() {
      const {pokemonName, pokemonImages, pokemonTypes,pokemonStats} = this.$refs;
      if (pokemonName, pokemonImages,pokemonTypes, pokemonStats) {
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
@import './styles.css';
@import url('https://fonts.googleapis.com/css2?family=Changa:wght@400;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');

type {
$normal: #a8a878;
$fire: #f08030;
$water: #6890f0;
$grass: #78c850;
$electric: #f8d030;
$ice: #98d8d8;
$fighting: #bf2f27;
$poison: #a040a0;
$ground: #e0c068;
$flying: #a890f0;
$psychic: #f85888;
$bug: #a8b820;
$rock: #b8a038;
$ghost: #705898;
$dark: #705848;
$dragon: #7038f8;
$steel: #b8b8d0;
$fairy: #f0b5bb;
$Arriba: gray;
$Descripcion: gray;
$fondoImg: black;
}
.main {
    background-color: type-of($value: type);
    border-radius: 10px;
    border: 10px solid gold; 
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
    padding: 20px;
    margin: 20px auto;
    max-width: 600px;
  } 
</style>