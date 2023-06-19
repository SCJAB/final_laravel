<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const currentPage = ref(1);
const perPage = 10;
const totalPages = ref(0);
const users = ref([]);
const pending = ref(false);

onMounted(fetchData);

async function previousPage() {
  if (currentPage.value > 1) {
    currentPage.value--;
    await fetchData();
  }
}

async function nextPage() {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
    await fetchData();
  }
}

async function fetchData() {
  pending.value = true;

  try {
    const response = await axios.get(`http://127.0.0.1:8000/api/users?page=${currentPage.value}&perPage=${perPage}`);
    users.value = response.data.data;
    totalPages.value = Math.ceil(response.data.total / perPage);
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    pending.value = false;
  }
}

function fetchDataByPage(page) {
  currentPage.value = page;
  fetchData();
}

function viewUser(userId) {
  navigateTo('/users/' + userId);
}

function editUser(userId) {
  navigateTo('/users/edit?user_id=' + userId);
}
</script>

<template>
  <div class="mx-auto max-w-5xl py-10">
    <h3 class="text-2xl">Users</h3>
    <div>
      <FormButton
        type="button"
        class="transition-colors duration-500 bg-violet-200 p-3 rounded-lg hover:bg-violet-400 hover:cursor-pointer hover:text-white text-violet-700"
        @click="navigateTo('/users/new')"
      >
        New User
      </FormButton>
    </div>
    <div>
      <div v-if="pending" class="flex justify-center text-7xl">
        <div class="loader"></div>
      </div>
      <div v-else>
        <table class="min-w-full text-left text-sm font-light">
          <thead class="border-b font-medium dark:border-neutral-500">
            <tr>
              <th scope="col" class="px-6 py-2">Name</th>
              <th scope="col" class="px-6 py-2">Course</th>
              <th scope="col" class="px-6 py-2">Is Active</th>
              <th scope="col" class="px-6 py-2">Action</th>
            </tr>
          </thead>
          <tbody v-if="users.length > 0">
            <tr class="border-b dark:border-neutral-500" v-for="user in users">
              <td class="whitespace-nowrap px-6 py-2 font-medium">{{ user.name }}</td>
              <td class="whitespace-nowrap px-6 py-2">{{ user.course }}</td>
              <td class="whitespace-nowrap px-6 py-2">
                <span v-if="user.is_active" class="text-violet-700">✓</span>
                <span v-else class="text-red-700 font-semibold">X</span>
              </td>
              <td class="whitespace-nowrap px-6 py-2">
                <a class="cursor-pointer hover:text-violet-700" @click="viewUser(user.id)">
                  View |
                </a>
                <a class="cursor-pointer hover:text-violet-700" @click="editUser(user.id)">
                  Edit
                </a>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="flex justify-between mt-10">
      <FormButtonPage
        type="button" class="transition-colors duration-500 bg-violet-200 p-3 rounded-lg hover:bg-violet-400 hover:cursor-pointer hover:text-white text-violet-700"
        @click="previousPage" :disabled="currentPage === 1"><span class="text-xl">&laquo;</span>  Previous</FormButtonPage>
      <FormButtonPage
        type="button" class="transition-colors duration-500 bg-violet-200 justify-end p-3 rounded-lg hover:bg-violet-400 hover:cursor-pointer hover:text-white text-violet-700"
        @click="nextPage" :disabled="currentPage === totalPages">Next  <span class="text-xl">&raquo;</span></FormButtonPage>
    </div>
  </div>
</template>

<style>
@keyframes loader {
  0% {
    content: '.';
  }
  33% {
    content: '..';
  }
  66% {
    content: '...';
  }
}

.loader::after {
  content: '...';
  animation: loader 1s steps(3) infinite;
}
</style>