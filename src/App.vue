<template>
  <div class="w-full flex flex-col items-center justify-center">
    <div class="my-5 w-full ml-40" >
      <input 
      class="border border-gray-300 rounded-lg p-2 w-[60%] md:w-[30%] focus:outline-none focus:ring-2 focus:ring-gray-300"
      v-model="filter"
      type="text"
     placeholder="Filtrar por nome"
     />
    </div>
 
    <div v-if="filteredProducts.length">
      <div class="grid grid-cols-2 md:grid-cols-3 md:gap-7 lg:grid-cols-4 gap-4 mx-3 max-w-7xl">
        <div v-for="product in paginatedProducts" :key="product.id"
          class="rounded-2xl border p-4 mb-4 shadow-lg hover:scale-110 duration-200 ">
          <img :src="product.src" alt="product.name" class="object-cover rounded-md hover:text" />
          <div class="flex items-center justify-evenly w-full my-3">
            <span class="text-sm bg-gray-200 font-black  p-1  border-black" v-for="variant in product.variants"
              :key="variant.id">
              {{ variant.value }} ({{ variant.stock }})
            </span>
          </div>
          <h2 class="text-md my-4">{{ product.name }}</h2>
          <p class="text-sm line-through">R$ {{ product.sale_price + 20.11 }}</p>
          <p class="text-green-600 text-lg "> R$ {{ product.sale_price.toFixed(2) }}</p>
          <p class="text-sm"> 2x de R$ {{ product.sale_price / 2 }} no cartão s/ juros</p>
        </div>
        <div v-if="loading" class="text-center">Carregando mais produtos...</div>
      </div>
      <button v-if="this.filteredProducts.length > 19" @click="nextPage" class="my-4 mx-3 p-2 bg-gray-500 hover:bg-gray-700 text-white text-sm rounded">Ver mais</button>
    </div>
    <div v-else >
      Carregando produtos...
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {

  data() {
    return {
      products: [],
      page: 1,
      limit: 20,
      filter: '',
      loading: false,
      offset: 0,
    };
  },
  computed: {

    filteredProducts() {
      return this.products.filter(product =>
        product.name.toLowerCase().includes(this.filter.toLowerCase())
      );
    },
    
    paginatedProducts() {
      console.log("paginatedProducts", this.filteredProducts);
      return this.filteredProducts.slice(0, this.page * this.limit);
    },

  },
  methods: {
    fetchProducts() {
      if (this.loading) return;
      this.loading = true;
      axios.post('https://apibgdev.nswebservice.com.br/store/10/products', {
        limit: 100,
        offset: this.offset
      })
        .then(response => {
          this.products = this.products.concat(response.data);
          this.offset += 100;  // Atualiza o offset para a próxima requisição
          this.loading = false;
        })
        .catch(error => {
          console.error('Erro ao buscar produtos:', error);
          this.loading = false;
        });
    },

    nextPage() {
      this.page++;
      this.fetchProducts();
    }
  },
  created() {
    this.fetchProducts();
  }
};
</script>

<style scoped>
</style>
