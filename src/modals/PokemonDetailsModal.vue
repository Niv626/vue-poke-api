<script setup>
const props = defineProps({
  pokemon: Object,
});

const emit = defineEmits(["close"]);

const closeModal = () => {
  props.pokemon.isModalOpen = false;
  emit("close");
};
</script>

<template>
  <Teleport to="body">
    <div class="modal">
      <div class="modal-overlay" @click="closeModal"></div>
      <div class="modal-content">
        <div class="pokemon-modal-title">
          <h1>{{ pokemon.name }}</h1>
        </div>
        <div class="modal-body">
          <div class="column-1">
            <h2>{{ pokemon.types.length > 1 ? "Types" : "Type" }}</h2>
            <ul>
              <li v-for="kind in pokemon.types" :key="kind.type.name">
                {{ kind.type.name }}
              </li>
            </ul>
            <h2>
              {{ pokemon.abilities.length > 1 ? "Abilities" : "Ability" }}
            </h2>
            <ul>
              <li
                v-for="ability in pokemon.abilities"
                :key="ability.ability.name"
              >
                {{ ability.ability.name }}
              </li>
            </ul>
          </div>
          <div class="column-2">
            <h2>General:</h2>
            <ul>
              <li>Height: {{ pokemon.height }}</li>
              <li>Weight: {{ pokemon.weight }}</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.modal-content {
  background-color: white;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  z-index: 2;
}

.modal-body {
  padding: 20px 80px 40px 80px;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
}

ul {
  list-style: none;
  padding-left: 0px;
  padding-bottom: 10px;
  font-weight: bold;
}

li {
  padding-bottom: 4px;
}

.column-1 {
  float: left;
  padding-right: 100px;
}

.column-2 {
  float: right;
}

.pokemon-modal-title {
  display: flex;
  justify-content: center;
  background-color: #f44da7;
}
</style>
