<script setup>
import axios from "axios";
import { store } from "./store.js";
import { ref, watch } from "vue";
import RecordModal from "./components/modal.vue";
const API_URL = import.meta.env.VITE_API_URL;
const loading = ref(false);
const res = ref(null);
const error = ref(null);
let table = ref("superheroes");
let recordId = ref("");
const helloWorld = ["Hello", "World", "With", "Vue", "3", "And", "Vite"];
const titleWords = ["Records", "Of", "Tables", "=>", "Activity 14", "<="];
const credits = ["By", "=>", "=>", "=>", "Rubencio", "<=", "<=", "<="];
let tableHeaders = ref([""]);
let tableContent = ref([""]);

function downloadFile(fileId) {
  console.log("fileId: ", fileId);
  axios.get(`${API_URL}/${table.value}/${fileId.toString()}`).then((res) => {
    if (!res.data) {
      return;
    }
    const fileData = res.data;
    const base64Data = fileData.file.split(",")[1];
    const mimeType = fileData.file.split(":")[1].split(";")[0];
    const fileName = fileData.file_name || "downloaded_file";
    const byteCharacters = atob(base64Data);
    const byteNumbers = new Array(byteCharacters.length);
    for (let i = 0; i < byteCharacters.length; i++) {
      byteNumbers[i] = byteCharacters.charCodeAt(i);
    }
    const byteArray = new Uint8Array(byteNumbers);
    const blob = new Blob([byteArray], { type: mimeType });
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = fileName + "." + mimeType.split("/")[1];
    document.body.appendChild(a);
    a.click();
    window.URL.revokeObjectURL(url);
    document.body.removeChild(a);
  });
}

watch(table, () => {
  getData();
});
watch(recordId, () => {
  getData();
});
getData();
async function getData() {
  loading.value = true;
  axios
    .get(`${API_URL}/${table.value}/${recordId.value.toString()}`)
    .then((res) => {
      if (!res.data) {
        return;
      }
      tableHeaders = res.data[0] ? res.data[0] : res.data;
      tableHeaders = Object.keys(tableHeaders).map((key) => {
        return key;
      });
      tableContent = res.data[0] ? res.data : [res.data];
      loading.value = false;
    });
}
function handleModal() {
  store.isModalOpen = !store.isModalOpen;
}
</script>
<template>
  <main class="min-h-screen min-w-full">
    <p class="flex items-start justify-center text-5xl font-extrabold my-4">
      <span
        v-for="(word, index) in helloWorld"
        :key="index"
        class="bg-blue-100 text-blue-800 text-2xl font-semibold me-2 px-2.5 py-0.5 rounded-sm dark:bg-blue-200 dark:text-blue-800 ms-2"
      >
        {{ word }}
      </span>
    </p>
    <p class="flex items-center justify-center text-5xl font-extrabold my-4">
      <span
        v-for="(word, index) in titleWords"
        :key="index"
        class="bg-blue-100 text-blue-800 text-2xl font-semibold me-2 px-2.5 py-0.5 rounded-sm dark:bg-blue-200 dark:text-blue-800 ms-2"
      >
        {{ word }}
      </span>
    </p>
    <p class="flex items-center justify-center text-5xl font-extrabold my-4">
      <span
        v-for="(word, index) in credits"
        :key="index"
        class="bg-blue-100 text-blue-800 text-2xl font-semibold me-2 px-2.5 py-0.5 rounded-sm dark:bg-blue-200 dark:text-blue-800 ms-2"
      >
        {{ word }}
      </span>
    </p>

    <div
      class="flex flex-col items-center justify-center mx-auto max-w-screen-xl min-w-full"
    >
      <div
        class="flex flex-row gap-4 items-end justify-end mt-8 mb-1 min-w-full"
      >
        <button
          @click="handleModal"
          class="border-2 border-white rounded-md p-2"
        >
          Add a new file
        </button>
        <select v-model="table" class="border-2 border-gray-500 rounded-md p-2">
          <option class="bg-gray-500" value="files">
            <span class="font-bold text-black">files</span>
          </option>
          <option class="bg-gray-500" value="superheroes">
            <span class="font-bold text-black">superheroes</span>
          </option>
          <option class="bg-gray-500" value="universes">
            <span class="font-bold text-black">universes</span>
          </option>
          <option class="bg-gray-500" value="genders">
            <span class="font-bold text-black">genders</span>
          </option>
        </select>
        <input
          type="number"
          class="border-2 border-gray-500 rounded-md p-1.5 ms-4"
          placeholder="search by id"
          v-model="recordid"
        />
      </div>

      <div v-if="loading" class="flex items-center justify-center my-8">
        Loading data...
      </div>
      <div
        v-else-if="error"
        class="flex items-center justify-center my-8 text-red-500"
      >
        Error loading data: {{ error.message }}
      </div>
      <div v-else class="container overflow-x-auto w-full">
        <table
          class="table-fixed border-collapse mt-4 bg-gray-100 shadow-md rounded-md w-full"
        >
          <thead class="bg-gray-200 w-full">
            <tr>
              <th
                v-for="(title, titleIndex) in tableHeaders"
                :key="titleIndex"
                class="px-4 py-2 text-center text-gray-700 font-bold uppercase border-b border-gray-300"
              >
                {{ title }}
              </th>
              <th
                class="px-4 py-2 text-center text-gray-700 font-bold uppercase border-b border-gray-300"
              >
                Actions
              </th>
            </tr>
          </thead>
          <tbody class="w-full">
            <tr
              v-for="(row, rowIndex) in tableContent"
              :key="rowIndex"
              class="hover:bg-gray-50"
            >
              <td
                v-for="(colValue, indexCol) in row"
                :key="indexCol"
                class="px-4 py-2 text-gray-600 border-b border-gray-300 w-24 overflow-hidden"
              >
                {{ colValue }}
              </td>
              <td
                v-if="table === 'files'"
                class="px-4 py-2 text-gray-600 border-b border-gray-300 w-24 overflow-hidden"
              >
                <button @click="downloadFile(row.id)" class="text-white">
                  Download
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div v-if="store.isModalOpen">
      <RecordModal />
    </div>
  </main>
</template>
