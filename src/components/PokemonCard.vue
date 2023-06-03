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
  <PokemonDetails
    v-if="pokemon.isModalOpen"
    @close="closeModal(index)"
    :pokemon="pokemon"
    :key="pokemon.name"
  />
</template>

<script>
import PokemonDetails from "../modals/PokemonDetails.vue";

export default {
  name: "PokemonCard",
  emits: ["close", "handleImageLoad"],
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
    closeModal() {
      this.$emit("close");
    },
    handleImageLoad() {
      this.$emit("handleImageLoad");
    },
  },
};
</script>
