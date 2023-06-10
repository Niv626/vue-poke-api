<template>
  <h1 class="home-title">
    <b>Pokémon World</b>
  </h1>

  <div class="pokemon-grid">
    <div
      v-for="(pokemon, index) in pokemons"
      class="pokemon-card"
      @click="openModal(index)"
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

<script>
import PokemonDetailsModal from "../modals/PokemonDetailsModal.vue";
import PokemonCard from "../components/PokemonCard.vue";

export default {
  components: {
    PokemonDetailsModal,
    PokemonCard,
  },

  data() {
    return {
      pokemons: [],
      isModalOpen: false,
      selectedItem: null,
      pokemon: {},
      isLoadingPokemons: false,
      error: null,
      offset: 0,
      totalNumOFPokemons: 0,
    };
  },
  methods: {
    async fetchPokemons() {
      this.isLoadingPokemons = true;
      try {
        const response = await fetch(
          `https://pokeapi.co/api/v2/pokemon?offset=${this.offset}&limit=50`
        );

        if (response.ok) {
          const pokemons = await response.json();
          this.totalNumOFPokemons = pokemons.count;
          let promises = [];
          for (const pokemon of pokemons.results) {
            promises.push(
              fetch(pokemon.url).then((response) => response.json())
            );
          }
          try {
            const data = await Promise.all(promises);
            data.forEach((item) => {
              this.pokemons.push({
                ...item,
                isModalOpen: false,
                isLoadingImage: true,
              });
            });
            this.isLoadingPokemons = false;
          } catch (error) {
            console.error("Error:", error);
          }
        } else {
          this.isLoadingPokemons = false;
          this.error = true;
        }
      } catch (error) {
        console.error(error);
        this.error = true;
      } finally {
        this.isLoadingPokemons = false;
      }

      this.offset += 50;
    },
    openModal(index) {
      this.pokemons[index].isModalOpen = true;
    },
  },

  async mounted() {
    this.fetchPokemons();
    let timeoutId = null;

    const handleScrollBottom = () => {
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => {
        if (
          window.innerHeight + Math.round(window.scrollY) >=
            document.body.offsetHeight &&
          this.totalNumOFPokemons >= this.offset
        ) {
          this.fetchPokemons();
        }
      }, 400);
    };

    window.addEventListener("scroll", handleScrollBottom);
  },
};
</script>

<style>
.pokemon-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, 200px);
  gap: 50px 30px;
  justify-content: space-evenly;
  padding-bottom: 50px;
  padding-top: 25px;
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
