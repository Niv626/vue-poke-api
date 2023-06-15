<script setup>
import HomePage from "./views/HomePage.vue";
import UsersList from "./views/UsersList.vue";
</script>

<template>
  <main class="main">
    <!-- <HomePage /> -->
    <UsersList></UsersList>
  </main>
</template>

<style>
html,
body {
  margin: 0;
  background-color: #f9f9f9;
  font-family: monospace;
  text-transform: capitalize;
  overflow-x: hidden;
}
</style>
