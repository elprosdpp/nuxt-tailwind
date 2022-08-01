<template>
  <div class="">
    <p>{{ items.title }}</p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  // delay for fetching
  // fetchDelay: 1000,
  data() {
    return {
      items: "",
    };
  },

  async fetch() {
    await this.getData();
  },

  head() {
    return {
      title: this.items.title,
    };
  },

  methods: {
    async getData() {
      const data = axios.get(`http://localhost:8000/api/blog/${this.$route.params.id}`);
      const result = await data;
      this.items = result.data.data;
    },
  },
};
</script>
