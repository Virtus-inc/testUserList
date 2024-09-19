<template>
  <div class="popup open">
    <div class="popup__body">
      <div class="popup__content">
        <a href="#" @click.prevent="closePopup" class="popup__close close-popup"
          >×</a
        >
        <div class="popup__title">
          <img :src="user.avatar" :alt="user.first_name" />
        </div>
        <div class="popup_text">
          <input v-model="user.first_name" type="text" />
          <input v-model="user.last_name" type="text" />
          <input v-model="user.email" type="email" />
        </div>
        <input
          type="submit"
          @click="saveChanges"
          name="submit"
          value="Зберігти"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, defineEmits, onMounted, onUnmounted, ref } from "vue";

const props = defineProps({
  user: Object,
});

const emit = defineEmits(["close", "update"]);

const isPopupVisible = ref(false);

const closePopup = () => {
  emit("close");
};

const saveChanges = async () => {
  try {
    const response = await fetch(
      `https://reqres.in/api/users/${props.user.id}`,
      {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          first_name: props.user.first_name,
          last_name: props.user.last_name,
          email: props.user.email,
        }),
      }
    );

    if (!response.ok) {
      throw new Error("Error updating user");
    }

    const updatedUser = await response.json();
    emit("update", updatedUser);
  } catch (error) {
    console.error("Error updating user:", error);
  }
};

const handleClickOutside = (event) => {
  if (!event.target.closest(".popup__content")) {
    closePopup();
  }
};

onMounted(() => {
  setTimeout(() => {
    isPopupVisible.value = true;
    document.addEventListener("click", handleClickOutside);
  }, 0);
});

onUnmounted(() => {
  document.removeEventListener("click", handleClickOutside);
});
</script>

<style scoped>
.popup {
  position: fixed;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8);
  top: 0;
  left: 0;
  opacity: 0;
  visibility: hidden;
}

.popup.open {
  opacity: 1;
  visibility: visible;
}

.popup__body {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}

.popup__content {
  border-radius: 7px;
  background-color: #fff;
  color: #000;
  max-width: 800px;
  padding: 30px;
  position: relative;
  opacity: 0;
}

.popup.open .popup__content {
  opacity: 1;
}

.popup__close {
  position: absolute;
  right: 10px;
  top: 10px;
  font-size: 20px;
  color: #000;
  text-decoration: none;
  cursor: pointer;
}

.popup__title img {
  display: block;
  margin: 1em auto 1em auto;
  max-width: 100%;
  height: auto;
}

.popup_text {
  display: flex;
  flex-direction: column;
}

input[type="text"],
input[type="email"],
select {
  width: 100%;
  padding: 12px 20px;
  margin: 8px 0;
  display: inline-block;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

input[type="submit"] {
  width: 100%;
  background-color: #4caf50;
  color: white;
  padding: 14px 20px;
  margin: 8px 0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

input[type="submit"]:hover {
  background-color: #45a049;
}
</style>
