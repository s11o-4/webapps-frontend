<script setup>
import { store } from "../store.js";
import { ref, watch } from "vue";
import axios from "axios";
const API_URL = import.meta.env.VITE_API_URL;

async function getBase64(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => {
      resolve(reader.result);
    };
    reader.onerror = (error) => {
      reject(error);
    };
  });
}
const file_title = ref("");
const file_description = ref("");
const file = ref("");

async function onFileChange(e) {
  const selectedFile = e.target.files[0];

  await getBase64(selectedFile).then((result) => {
    file.value = result;
    return result;
  });
}

async function submitForm() {
  const body = {
    file_name: file_title.value,
    description: file_description.value,
    file: file.value,
  };

  axios
    .post(`${API_URL}/files`, body, {})
    .then((res) => {
      console.log(res.data);
    })
    .catch((err) => {
      console.error(err);
    })
    .finally(() => {
      store.isModalOpen = false;
    });
}

function handleModalClose() {
  store.isModalOpen = false;
}
</script>

<template>
  <div
    class="relative z-10"
    aria-labelledby="modal-title"
    role="dialog"
    aria-modal="true"
  >
    <div
      class="fixed inset-0 bg-gray-500/75 transition-opacity"
      aria-hidden="true"
    ></div>

    <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
      <div
        class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0"
      >
        <div
          class="relative transform overflow-hidden rounded-lg bg-white text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg"
        >
          <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4 w-full">
            <div class="sm:flex flex-col sm:items-start">
              <div class="mt-3 text-center sm:mt-0 text-left">
                <span
                  class="font-semibold text-gray-900 text-2xl"
                  id="modal-title"
                >
                  Add a new file
                </span>
                <form @submit.prevent="submitForm">
                  <div class="mt-2 flex flex-row items-start w-full gap-2">
                    <div>
                      <input
                        type="text"
                        class="border-2 border-gray-500 rounded-md p-2 text-gray-900"
                        placeholder="title"
                        v-model="file_title"
                      />
                    </div>
                    <div>
                      <input
                        type="text"
                        class="border-2 border-gray-500 rounded-md p-2 ms-4 text-gray-900"
                        placeholder="description"
                        v-model="file_description"
                      />
                    </div>
                  </div>
                  <div class="flex flex-row items-center min-w-full pt-4">
                    <input
                      type="file"
                      class="border-2 border-gray-500 rounded-md p-2 text-gray-900 min-w-full"
                      @change="onFileChange"
                    />
                  </div>
                </form>
              </div>
            </div>
          </div>
          <div
            class="bg-gray-50 px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6 items-end align-items-end w-full"
          >
            <button
              type="submit"
              @click="submitForm"
              class="inline-flex w-full justify-center flex-1/2 grow-0 rounded-md px-3 py-2 text-sm font-semibold text-white shadow-xs sm:ml-3 sm:w-auto"
            >
              Register
            </button>
            <button
              @click="handleModalClose"
              type="button"
              class="flex-1/2 grow-0 mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-xs ring-1 ring-gray-300 ring-inset hover:bg-gray-50 sm:mt-0 sm:w-auto text-white"
            >
              Cancel
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
