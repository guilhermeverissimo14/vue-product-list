<template>
  <div>
    <input
      v-model="filter"
      @input="fetchProducts"
      class="mb-4 p-2 border border-gray-300"
      type="text"
      placeholder="Filter by name"
    />
    <div class="grid grid-cols-1 gap-4">
      <ProductItem
        v-for="product in filteredProducts"
        :key="product.id"
        :product="product"
      />
    </div>
    <div class="mt-4 flex justify-between items-center">
      <button
        :disabled="page === 1"
        @click="changePage(page - 1)"
        class="px-4 py-2 bg-gray-300 hover:bg-gray-400 rounded"
      >
        Previous
      </button>
      <span>Page {{ page }}</span>
      <button
        :disabled="!hasMore"
        @click="changePage(page + 1)"
        class="px-4 py-2 bg-gray-300 hover:bg-gray-400 rounded"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import ProductItem from './ProductItem.vue';

export default {
  components: {
    ProductItem
  },
  data() {
    return {
      products: [],
      filter: '',
      page: 1,
      limit: 10,
      hasMore: true
    };
  },
  computed: {
    filteredProducts() {
      return this.products.filter(product =>
        product.name.toLowerCase().includes(this.filter.toLowerCase())
      );
    }
  },
  methods: {
    async fetchProducts() {
      try {
        const response = await axios.post('https://apibgdev.nswebservice.com.br/store/10/products', {
          limit: this.limit,
          page: this.page
        });
        this.products = response.data;
        this.hasMore = response.data.length === this.limit;
      } catch (error) {
        console.error(error);
      }
    },
    changePage(newPage) {
      this.page = newPage;
      this.fetchProducts();
    }
  },
  mounted() {
    this.fetchProducts();
  }
};
</script>

<style scoped>
</style>
