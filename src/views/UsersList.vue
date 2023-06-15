<script setup>
import { ref, computed, watch } from "vue";

const newUser = ref({ name: "", email: "", age: null });
const usersList = ref([]);
const errorMsg = ref(null);
const isLoading = ref(false);
const search = ref("");

const submmitUser = async () => {
  let result = {};
  try {
    isLoading.value = true;
    const res = await fetch("https://test-api-lfod.onrender.com/users", {
      method: "POST",
      body: JSON.stringify(newUser.value),
      headers: {
        "Content-Type": "application/json",
      },
    });

    result = await res.json();

    if (result.error) {
      errorMsg.value = result.error;
    }
  } catch (e) {
    console.error(e);
  }
  isLoading.value = false;

  usersList.value.push(result);
  newUser.value = { name: "", email: "", age: null };
};

const isDisabled = computed(() => {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  // checks if one of them is empty
  const isEmpty = !(
    newUser.value.email &&
    newUser.value.age &&
    newUser.value.name
  );
  // valid email and above or equel 18
  return regex.test(newUser.value.email) && newUser.value.age >= 18 && !isEmpty;
});

// const filterUsers = computed(() => {
//   if (search === "") return usersList.value;
//   const val = usersList.value.filter((user) => {
//     console.log("user", user);
//     return user.name.toLowerCase().includes(search.toLowerCase);
//   });
//   return val;
// });
</script>

<template>
  <h1 class="users-list-title"><b>Users List</b></h1>
  <!-- <input type="search" v-model="search" /> -->
  <div class="form">
    <label for="name">Name:</label>
    <input v-model="newUser.name" type="text" id="name" placeholder="Name..." />

    <label for="email">Email:</label>
    <input
      v-model="newUser.email"
      type="email"
      id="email"
      placeholder="Email..."
    />

    <label for="age">Age:</label>
    <input
      v-model="newUser.age"
      type="number"
      id="age"
      min="0"
      placeholder="Age..."
    />

    <button
      :disabled="!isDisabled"
      class="submmit-user-btn"
      @click="submmitUser"
    >
      Add User
    </button>

    <div class="error-msg">{{ errorMsg }}</div>
  </div>
  <div v-show="isLoading">Loading users...</div>

  <div class="users-table">
    <table
      v-show="usersList.length > 0"
      class="table table-striped table-bordered"
    >
      <thead>
        <tr>
          <th>Name</th>
          <th>Email</th>
          <th>Age</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in usersList" :key="user.id">
          <td>{{ user.name }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.age }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.form {
  display: flex;
  align-items: flex-start;
  margin: 10px;
  flex-direction: column;
  margin-left: 50px;
}

.submmit-user-btn {
  padding: 10px;
  margin: 10px;
}

.submmit-user-btn {
  border-radius: 10px;
  background-color: #f44da7;
}
.error-msg {
  color: red;
}

h1 {
  display: flex;
  justify-content: center;
  color: #f44da7;
}

table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td,
th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}

.users-table {
  margin-right: 50px;
  margin-left: 50px;
}

input,
label {
  width: 25%;
  height: 18px;
  /* font-size: x-large; */
  margin: 5px;
}
label {
  font-size: x-large;
  font-weight: bold;
}
</style>
