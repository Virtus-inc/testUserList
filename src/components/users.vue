<script setup lang="ts">
import { ref, onMounted } from "vue";

const users = ref<any[]>([]);

const fetchUsers = async () => {
  try {
    const response = await fetch("https://reqres.in/api/users");
    const data = await response.json();

    users.value = data.data;
  } catch (error) {
    console.error("Error fetching users:", error);
  }
};

onMounted(() => {
  fetchUsers();
});
</script>

<template>
  <div>
    <h1 class="text-center">Hello ReqRes users!</h1>
    <div>
      <div class="flex" v-if="users.length">
        <div v-for="user in users" :key="user.id">
          <img :src="user.avatar" :alt="user.first_name" />
          <p>
            <strong>{{ user.first_name }} {{ user.last_name }}</strong>
          </p>
          <p>{{ user.email }}</p>
        </div>
      </div>
    </div>
  </div>
</template>
