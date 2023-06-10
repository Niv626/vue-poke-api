<template>
  <div :key="pokemon.name">
    <div style="height: 96px; width: 96px">
      <div v-show="pokemon.isLoadingImage" class="loading">Loading Image</div>
      <img
        :alt="pokemon.name"
        @load="handleImageLoad"
        :src="pokemon.sprites.front_default"
      />
    </div>
    <h2 class="pokemon-title-card">{{ pokemon.name }}</h2>
  </div>
  <slot></slot>
</template>

<script>
import PokemonDetails from "../modals/PokemonDetailsModal.vue";

export default {
  name: "PokemonCard",
  components: {
    PokemonDetails,
  },
  props: ["pokemon"],
  data() {
    return {
      showModal: false,
      selectedPokemon: null,
      isLoadinImage: true,
    };
  },
  methods: {
    handleImageLoad() {
      this.pokemon.isLoadingImage = false;
    },
  },
};
</script>
