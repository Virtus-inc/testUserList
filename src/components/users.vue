<template>
  <div class="cover">
    <h1 class="text-center">Hello ReqRes users!</h1>
    <div v-if="users.length" class="flex">
      <div v-for="user in users" :key="user.id" @click="openPopup(user)">
        <img :src="user.avatar" :alt="user.first_name" />
        <p>
          <strong>{{ user.first_name }} {{ user.last_name }}</strong>
        </p>
        <p>{{ user.email }}</p>
      </div>
    </div>

    <popup v-if="isPopupOpen" :user="selectedUser" @close="closePopup" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import popup from "./popup.vue";

const users = ref<any[]>([]);
const selectedUser = ref(null);
const isPopupOpen = ref(false);

const fetchUsers = async () => {
  try {
    const response = await fetch("https://reqres.in/api/users");
    const data = await response.json();
    users.value = data.data;
  } catch (error) {
    console.error("Error fetching users:", error);
  }
};

const openPopup = (user) => {
  selectedUser.value = user;
  isPopupOpen.value = true;
  // Добавляем слушатель для закрытия по Esc
  document.addEventListener("keydown", handleEscClose);
};

const closePopup = () => {
  isPopupOpen.value = false;
  // Убираем слушатель для закрытия по Esc
  document.removeEventListener("keydown", handleEscClose);
};

const handleEscClose = (event) => {
  if (event.key === "Escape") {
    closePopup();
  }
};

onMounted(() => {
  fetchUsers();
});

onUnmounted(() => {
  document.removeEventListener("keydown", handleEscClose);
});
</script>

<style scoped>
.cover {
  width: 40%;
  margin: 0 auto;
  font-weight: normal;
}

.flex {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
}

.flex > div {
  margin: 0 1rem 2rem 1rem;
  text-align: center;
}

.text-center {
  text-align: center;
  margin-bottom: 1em;
}

img {
  border-radius: 7px;
}
</style>
