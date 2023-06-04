<template>
  <div class="pokemon-image-card">
    <div style="height: 96px; width: 96px">
      <div v-if="pokemon.isLoadingImage" class="loading">Loading Image</div>
      <img
        v-show="!pokemon.isLoadingImage"
        :alt="pokemon.name"
        @load="handleImageLoad(index)"
        :src="pokemon.sprites.front_default"
      />
    </div>
  </div>
  <h2 class="pokemon-title-card">{{ pokemon.name }}</h2>
  <slot></slot>
</template>

<script>
import PokemonDetails from "../modals/PokemonDetails.vue";

export default {
  name: "PokemonCard",
  emits: ["handleImageLoad"],
  components: {
    PokemonDetails,
  },
  props: ["pokemon", "index"],
  data() {
    return {
      showModal: false,
      selectedPokemon: null,
    };
  },
  methods: {
    handleImageLoad() {
      this.$emit("handleImageLoad");
    },
  },
};
</script>
