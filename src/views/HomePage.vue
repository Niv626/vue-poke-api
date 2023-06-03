<template>
  <div class="home-title">
    <b>Pokémon World</b>
  </div>
  <div class="pokemon-grid">
    <div
      v-for="(pokemon, index) in pokemons"
      class="pokemon-card"
      @click="openModal(index)"
      :key="pokemon.id"
      v-if="!isLoadingPokemons"
    >
      <PokemonCard
        :key="pokemon.id"
        :pokemon="pokemon"
        v-if="!isLoadingPokemons"
        @handleImageLoad="handleImageLoad(index)"
        @close="closeModal(index)"
      ></PokemonCard>
    </div>

    <h2 v-else class="loading loading-pokemons">
      <div v-if="pokemons.length > 0">Loading More Pokemons</div>
      <div v-else>Loading Pokemons</div>
    </h2>
    <h2 v-if="error" style="width: max-content">Failed To Load Page</h2>
  </div>
</template>

<script>
import PokemonDetails from "../modals/PokemonDetails.vue";
import PokemonCard from "../components/PokemonCard.vue";

export default {
  components: {
    PokemonDetails,
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
    };
  },
  methods: {
    async fetchPokemons() {
      this.isLoadingPokemons = true;
      const response = await fetch(
        `https://pokeapi.co/api/v2/pokemon?offset=${this.offset}&limit=50`
      ).catch(() => {
        this.error = true;
        this.isLoadingPokemons = false;
        return;
      });

      if (response.ok) {
        this.isLoadingPokemons = false;
        const pokemons = await response.json();
        pokemons.results.forEach(async (pokemon) => {
          const res = await fetch(pokemon.url);
          if (res.ok) {
            const data = await res.json();
            this.pokemons = [
              ...this.pokemons,
              {
                ...data,
                isModalOpen: false,
                isLoadingImage: true,
              },
            ];
          } else {
            console.error("error", res);
          }
        });
      } else {
        this.isLoadingPokemons = false;
        this.error = true;
      }
      if (this.offset > 0) {
        window.scrollTo(
          5,
          document.documentElement.scrollHeight || document.body.scrollHeight
        );
      }
      this.offset += 50;
    },

    openModal(index) {
      this.pokemons[index].isModalOpen = true;
    },
    closeModal(index) {
      this.pokemons[index].isModalOpen = false;
    },
    handleImageLoad(index) {
      this.pokemons[index].isLoadingImage = false;
    },
  },

  async mounted() {
    this.fetchPokemons();
    let timeoutId = null;

    const handleScroll = () => {
      clearTimeout(timeoutId);

      timeoutId = setTimeout(() => {
        if (
          window.innerHeight + Math.round(window.scrollY) >=
          document.body.offsetHeight
        ) {
          this.fetchPokemons();
        }
      }, 200);
    };

    window.addEventListener("scroll", handleScroll);
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
  background-image: radial-gradient(circle, #4c0bff, #f44da7);
  color: transparent;
  -webkit-background-clip: text;
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
  min-width: 242px;
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
.loading .loading-pokemons {
  padding-top: 150px;
  padding-left: 10px;
}

.home-title {
  padding-top: 17px;
}
</style>
