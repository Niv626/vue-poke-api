<script setup>
import { ref, reactive, onMounted, computed, watch, watchEffect } from "vue";
import PokemonDetailsModal from "../modals/PokemonDetailsModal.vue";
import PokemonCard from "../components/PokemonCard.vue";

const pokemons = ref([]);

const isLoadingPokemons = ref(false);
const error = ref(null);
const offset = ref(0);
const totalNumOFPokemons = ref(0);

const fetchPokemons = async () => {
  isLoadingPokemons.value = true;
  try {
    const response = await fetch(
      `https://pokeapi.co/api/v2/pokemon?offset=${offset.value}&limit=50`
    );

    if (response.ok) {
      const pokemonsData = await response.json();
      totalNumOFPokemons.value = pokemonsData.count;
      const promises = pokemonsData.results.map((pokemon) =>
        fetch(pokemon.url).then((response) => response.json())
      );
      try {
        const data = await Promise.all(promises);
        data.forEach((item) => {
          pokemons.value.push({
            ...item,
            isModalOpen: false,
            isLoadingImage: true,
          });
        });
        isLoadingPokemons.value = false;
      } catch (error) {
        console.error("Error:", error);
      }
    } else {
      isLoadingPokemons.value = false;
      error.value = true;
    }
  } catch (error) {
    console.error(error);
    error.value = true;
  } finally {
    isLoadingPokemons.value = false;
  }

  offset.value += 50;
};

const openModal = (id) => {
  const index = pokemons.value.findIndex((pokemon) => pokemon.id === id);
  pokemons.value[index].isModalOpen = true;
};

onMounted(() => {
  fetchPokemons();
  let timeoutId = null;

  const handleScrollBottom = () => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => {
      if (
        window.innerHeight + Math.round(window.scrollY) >=
          document.body.offsetHeight &&
        totalNumOFPokemons.value >= offset.value
      ) {
        fetchPokemons();
      }
    }, 400);
  };

  window.addEventListener("scroll", handleScrollBottom);
});
</script>

<template>
  <h1 class="home-title">
    <b>Pokémon World</b>
  </h1>
  <div class="pokemon-grid">
    <div
      v-for="(pokemon, index) in pokemons"
      class="pokemon-card"
      @click="openModal(pokemon.id)"
      :key="pokemon.id"
    >
      <PokemonCard :key="pokemon.name" :pokemon="pokemon">
        <PokemonDetailsModal
          v-if="pokemon.isModalOpen"
          :key="index"
          :pokemon="pokemon"
        />
      </PokemonCard>
    </div>
  </div>

  <div v-show="isLoadingPokemons" class="loading-pokemons">
    <h2 :class="['loading', 'loading-pokemons']">
      Loading {{ pokemons.length >= offset && "More" }} Pokemons
    </h2>
  </div>
  <h2 v-if="error" style="width: max-content">Failed To Load Page</h2>
</template>

<style>
.pokemon-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, 200px);
  gap: 50px 30px;
  justify-content: space-evenly;
  padding-bottom: 50px;
  padding-top: 25px;
  padding-inline: 90px;
}

.home-title {
  display: flex;
  justify-content: center;
  font-size: 50px;
  color: #f44da7;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
  -webkit-text-stroke: 2px #000; /* Outline color and width */
}

.pokemon-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: white;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
  padding: 40px;
  cursor: pointer;
  max-height: 120px;
}

.pokemon-card:hover {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}

.loading {
  min-width: 250px;
}
.loading::after {
  display: inline-block;
  animation: dotty steps(1, end) 1s infinite;
  content: "";
}

@keyframes dotty {
  0% {
    content: "";
  }
  25% {
    content: ".";
  }
  50% {
    content: "..";
  }
  75% {
    content: "...";
  }
  100% {
    content: "";
  }
}
.loading-pokemons {
  display: flex;
}

.home-title {
  padding-top: 17px;
}

.loading-pokemons {
  display: flex;
  justify-content: center;
}
</style>
