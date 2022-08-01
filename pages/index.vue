<template>
  <div class="container mx-auto py-20">
    <div class="flex flex-wrap justify-between items-center pb-5">
      <div class="">
        <label
          for="default-search"
          class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-gray-300"
          >Search</label
        >
        <div class="relative">
          <div
            class="flex absolute inset-y-0 left-0 items-center pl-3 pointer-events-none"
          >
            <svg
              class="w-5 h-5 text-gray-500 dark:text-gray-400"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
              ></path>
            </svg>
          </div>
          <input
            v-model="search"
            type="search"
            class="block p-4 pl-10 w-80 text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 lg:w-[25rem]"
            placeholder="Cari Berita"
            required
            @keyup.enter="getData"
          />
        </div>
      </div>
      <div class="">
        <select
          class="bg-gray-50 my-4 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-4 lg:ml-5 lg:my-0"
          @change="changeItem($event.target.value)"
        >
          <option value="">Semua Kategori</option>
          <option v-for="item in category" :key="item.id" :value="item.slug_category">
            {{ item.category }}
          </option>
        </select>
      </div>
      <div class="inline-flex xs:mt-0">
        <button
          class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200"
          :disabled="pages > 1 ? disabled : ''"
          @click="getData(pages - 1)"
        >
          previous
        </button>
        <button
          class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200"
          :disabled="pages < lastPage ? disabled : ''"
          @click="getData(pages + 1)"
        >
          next
        </button>
      </div>
    </div>

    <div v-if="!items || !items.length">Data Tidak Ditemukan</div>
    <div v-else class="flex flex-wrap justify-center items-center">
      <div
        v-for="item in items"
        :key="item.id"
        class="h-[25rem] mb-5 bg-white rounded-lg border-2 shadow-sm cursor-pointer md:mx-3 md:w-96 lg:w-[31.2%] lg:h-[28rem] mx-5 my-5"
      >
        <NuxtLink :to="{ name: 'mountain-id', params: { id: item.id } }">
          <img
            class="p-3 w-full h-64 object-cover rounded-[1.2rem]"
            :src="item.image"
            :alt="item.title"
          />
        </NuxtLink>

        <div class="p-5">
          <NuxtLink :to="{ name: 'mountain-id', params: { id: item.id } }">
            <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900">
              {{ item.title }}
            </h5>
          </NuxtLink>
          <p class="mb-3 font-normal text-gray-700 dark:text-gray-400">
            {{ item.description.substring(0, 40) + ".." }}
          </p>
          <NuxtLink
            :to="{ name: 'mountain-id', params: { id: item.id } }"
            class="inline-flex items-center py-2 px-3 text-sm font-medium text-center text-white bg-blue-700 rounded-lg hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
          >
            Read more
            <svg
              aria-hidden="true"
              class="ml-2 -mr-1 w-4 h-4"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M10.293 3.293a1 1 0 011.414 0l6 6a1 1 0 010 1.414l-6 6a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-4.293-4.293a1 1 0 010-1.414z"
                clip-rule="evenodd"
              ></path>
            </svg>
          </NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "IndexPage",
  data() {
    return {
      items: [],
      pages: 1,
      lastPage: "",
      disabled: null,
      search: "",
      category: [],
      selected: "",
    };
  },

  async fetch() {
    await this.getData();
    await this.getCategory();
  },

  fetchDelay: 1000,

  head() {
    return {
      title: "Blog",
    };
  },

  watch: {
    search() {
      this.getData();
    },
    selected() {
      this.getData();
    },
  },

  methods: {
    async getData(pages) {
      const data = axios.get("http://localhost:8000/api/blog", {
        params: {
          page: pages,
          s: this.search,
          k: this.selected,
        },
      });
      const result = await data;
      this.items = result.data.data;
      this.pages = result.data.meta.current_page;
      this.lastPage = result.data.meta.last_page;
    },

    async getCategory() {
      const data = axios.get("http://localhost:8000/api/category");
      const result = await data;
      this.category = result.data.data;
    },

    changeItem(event) {
      this.selected = event;
    },
  },
};
</script>
