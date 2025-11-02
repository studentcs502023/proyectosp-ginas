<template>
  <div class="app">
    <h1 class="titulo">PokéDex Vue</h1>

    <div class="buscador">
      <input
        type="text"
        v-model="apicode"
        placeholder="Escribe un nombre de Pokémon"
      />
      <button @click="buscarPokemon">Buscar</button>
    </div>

    <div v-if="error" class="error">{{ error }}</div>

    <div v-if="pokemon && pokemon.sprites" class="container">
      <!-- Info principal -->
      <div class="imag">
        <h2 class="nombre">{{ pokemon.name }}</h2>






        <img :src="pokemon.sprites.other.showdown.front_default" alt="pokemon" />
        <p>#{{ pokemon.id }}</p>
        <p>Peso: {{ pokemon.weight }} | Altura: {{ pokemon.height }}</p>

        <div class="tipos">
          <span
            v-for="(it, i) in pokemon.types"
            :key="i"
            :style="color(it.type.name)"
            class="tipo"
          >
            {{ it.type.name }}
          </span>
        </div>
      </div>

      <!-- Debilidades -->
      <div class="debilidades" v-if="weak.length > 0">
        <h3>Debilidades:</h3>
        <ul>
          <li
            v-for="(d, i) in weak"
            :key="i"
            :style="color(d.name)"
            class="weak-item"
          >
            {{ d.name }}
          </li>
        </ul>
      </div>

      <!-- Estadísticas -->
      <div class="stats">
        <h3>Estadísticas</h3>
        <div
          v-for="(it, i) in pokemon.stats"
          :key="i"
          class="stat-bar"
        >
          <span class="stat-name">{{ it.stat.name }}</span>
          <div class="bar-bg">
            <div
              class="bar-fill"
              :style="barraColor(it.base_stat)"
            >
              {{ it.base_stat }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-if="pokeball" class="pokeball activo">
      <div class="line"></div>
      <div class="button"></div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const tipoColor = {
  normal: "#A8A77A",
  fire: "#EE8130",
  water: "#6390F0",
  electric: "#F7D02C",
  grass: "#7AC74C",
  ice: "#96D9D6",
  fighting: "#C22E28",
  poison: "#A33EA1",
  ground: "#E2BF65",
  flying: "#A98FF3",
  psychic: "#F95587",
  bug: "#A6B91A",
  rock: "#B6A136",
  ghost: "#735797",
  dragon: "#6F35FC",
  dark: "#705746",
  steel: "#B7B7CE",
  fairy: "#D685AD",
};

const pokeball = ref(false);
const apicode = ref("");
const pokemon = ref(null);
const error = ref("");
const url = ref(null);
const weak = ref([]);
const mainColor = ref("#ccc");
const secondaryColor = ref(null);

async function buscarPokemon() {
  try {
    error.value = "";
    pokemon.value = null;
    weak.value = [];

    if (!apicode.value) {
      error.value = "Por favor ingresa el nombre de un Pokémon";
      return;
    }

    const { data } = await axios.get(
      `https://pokeapi.co/api/v2/pokemon/${apicode.value.toLowerCase()}`
    );
    pokemon.value = data;

    const tipos = pokemon.value.types.map((t) => t.type.name);
    mainColor.value = tipoColor[tipos[0]] || "#999";
    secondaryColor.value = tipos[1] ? tipoColor[tipos[1]] : null;

    url.value = pokemon.value.types[0].type.url;
    const resWeak = await axios.get(url.value);
    weak.value = resWeak.data.damage_relations.double_damage_from;
    pokeball.value = true;
  } catch (err) {
    error.value = "Pokémon no encontrado o error en la petición";
    console.error(err);
  }
}

function color(typeName) {
  const colorBase = tipoColor[typeName];
  return {
    backgroundColor: colorBase ? colorBase : "#888",
    color: "white",
    padding: "6px 12px",
    borderRadius: "10px",
    textShadow: "1px 1px 3px black",
    margin: "4px",
    display: "inline-block",
  };
}

function barraColor(statValue) {
  const percent = Math.min(statValue, 150);
  const bg =
    secondaryColor.value
      ? `linear-gradient(90deg, ${mainColor.value}, ${secondaryColor.value})`
      : mainColor.value;
  return {
    width: `${percent}px`,
    background: bg,
    color: "white",
    borderRadius: "10px",
    padding: "3px",
    animation: "fill 0.8s ease-out",
  };
}
</script>

<style scoped>
.app {
  text-align: center;
  background: linear-gradient(135deg, #2c2c2c, #121212);
  min-height: 100vh;
  color: white;
  padding: 20px;
  font-family: "Poppins", sans-serif;
}

.titulo {
  font-size: 2em;
  margin-bottom: 10px;
  text-shadow: 0 0 10px #fff;
  animation: glow 2s infinite alternate;
}

.buscador {
  margin-bottom: 25px;
  display: flex;
  justify-content: center;
  gap: 10px;
}

input {
  padding: 8px;
  border-radius: 10px;
  border: none;
  width: 200px;
}

button {
  padding: 8px 16px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  background: linear-gradient(90deg, #ff1c1c, #ff9900);
  color: white;
  transition: 0.3s;
}
button:hover {
  transform: scale(1.05);
}

.error {
  color: red;
  margin-bottom: 10px;
}

.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
  padding: 10px;
  text-align: center;
}

.imag img {
  width: 120px;
  transition: transform 0.4s;
}
.imag img:hover {
  transform: scale(1.2);
}

.debilidades,
.stats {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 20px;
  padding: 10px;
  backdrop-filter: blur(6px);
}

.stat-bar {
  margin: 10px 0;
}

.bar-bg {
  background: rgba(255, 255, 255, 0.15);
  height: 22px;
  border-radius: 10px;
  overflow: hidden;
  margin-top: 4px;
}

@keyframes fill {
  from {
    width: 0;
  }
}

@keyframes glow {
  from {
    text-shadow: 0 0 5px #fff;
  }
  to {
    text-shadow: 0 0 20px #ff1c1c, 0 0 30px #ff9900;
  }
}

/* Pokeball */
.pokeball {
  position: fixed;


  bottom: 20px;
  left: 20px;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background: white;
  overflow: hidden;
  box-shadow: 0 0 40px rgba(0, 0, 0, 0.8);
  animation: spin 2s infinite linear;
}

.pokeball::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 50%;
  background: #ff1c1c;
}

.line {
  position: absolute;
  top: 50%;
  left: 0;
  width: 100%;
  height: 10px;
  background: black;
  transform: translateY(-50%);
}

.button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 25px;
  height: 25px;
  border-radius: 50%;
  background: white;
  border: 5px solid black;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
