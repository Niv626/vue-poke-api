<template>
  <Teleport to="body">
    <div class="modal">
      <div class="modal-overlay" @click="closeModal"></div>
      <div class="modal-content">
        <!-- <slot></slot> -->
        <div class="pokemon-modal-title">
          <h1>{{ pokemon.name }}</h1>
        </div>
        <div class="modal-body">
          <div class="column-1">
            <ul>
              <li v-if="pokemon.types.length > 1">
                <ul>
                  <h2>Types:</h2>
                  <li v-for="kind in pokemon.types">
                    {{ kind.type.name }}
                  </li>
                </ul>
              </li>
              <div v-else>
                <h2>Type:</h2>
                <li>{{ pokemon.abilities?.[0].ability?.name }}</li>
              </div>
              <li v-if="pokemon.abilities.length > 1">
                <ul>
                  <h2>Abilities:</h2>
                  <li v-for="ability in pokemon.abilities">
                    {{ ability.ability.name }}
                  </li>
                </ul>
              </li>
              <div v-else>
                <h2>Abilitiy:</h2>
                <li>{{ pokemon.abilities[0]?.ability?.name }}</li>
              </div>
            </ul>
          </div>
          <div class="column-2">
            <ul>
              <h2>General:</h2>
              <li>Height: {{ pokemon.height }}</li>
              <li>Weight: {{ pokemon.weight }}</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<script>
export default {
  name: "PokemonDetails",
  props: ["pokemon"],
  emits: ["close"],
  methods: {
    closeModal() {
      this.$emit("close");
    },
  },
};
</script>

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
