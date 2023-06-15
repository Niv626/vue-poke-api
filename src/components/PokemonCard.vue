<script setup>
import { ref } from "vue";

defineProps({
  pokemon: Object,
});

const isLoadinImage = ref(true);

const handleImageLoad = () => {
  isLoadinImage.value = false;
};
</script>

<template>
  <div :key="pokemon.name">
    <div style="height: 96px; width: 96px">
      <div v-show="isLoadinImage" class="loading">Loading Image</div>
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
