<!-- eslint-disable max-len -->
<!-- eslint-disable vuejs-accessibility/label-has-for -->
<template>
  <main class="content container">
    <div class="content__top">

      <div class="content__row">
        <h1 class="content__title">
          Каталог
        </h1>
        <span class="content__info">
          {{ countProducts }} товара
        </span>
      </div>
      <fieldset class="form__block">
        <legend class="form__legend">Количество товаров на странице</legend>
        <label class="form__label form__label--select">
          <select class="form__select" type="text" name="category"
          v-model.number="productsPerPage">
            <option :value="p" v-for="p in [9, 12, 18, 24, 27, 32]"
            :key="p">{{ p }}</option>
          </select>
        </label>
      </fieldset>
    </div>

    <div class="content__catalog">
      <ProductFilter :price-from.sync="filterPriceFrom" :price-to.sync="filterPriceTo"
        :category-id.sync="filterCategoryId" :color-ids.sync="filterColorIds"
        :material-ids.sync="filterMaterialIds" :season-ids.sync="filterSeasonIds"/>

      <section class="catalog">
        <fieldset class="form__block">
          <legend class="form__legend">Количество товаров на странице</legend>
          <label class="form__label form__label--select">
            <select class="form__select" type="text" name="category"
            v-model.number="productsPerPage">
              <option :value="p" v-for="p in [9, 12, 18, 24, 27, 32]"
              :key="p">{{ p }}</option>
            </select>
          </label>
        </fieldset>

        <div v-if="productsLoadingFailed">
          <h2 style="text-align: center;">Произошла ошибка при загрузке товаров
            <button @click.prevent="loadProducts">Попробовать еще раз</button>
          </h2>
        </div>

        <div v-else-if="productsLoading">
          <h2 style="text-align: center;">Загрузка товаров...</h2>
          <br><br><br><br><br>
          <div class="windows8">
            <div class="wBall" id="wBall_1">
              <div class="wInnerBall"></div>
            </div>
            <div class="wBall" id="wBall_2">
              <div class="wInnerBall"></div>
            </div>
            <div class="wBall" id="wBall_3">
              <div class="wInnerBall"></div>
            </div>
            <div class="wBall" id="wBall_4">
              <div class="wInnerBall"></div>
            </div>
            <div class="wBall" id="wBall_5">
              <div class="wInnerBall"></div>
            </div>
          </div>
        </div>

        <div v-else-if="products.length === 0">
          <h2 style="text-align: center;">Таких товаров нет!</h2>
        </div>

        <ProductList v-else :products="products" />
        <BasePagination v-model="page" :count="countProducts" :per-page="productsPerPage" />
      </section>
    </div>
  </main>
</template>

<script>
import ProductList from '@/components/ProductList.vue';
import BasePagination from '@/components/BasePagination.vue';
import ProductFilter from '@/components/ProductFilter.vue';
import axios from 'axios';
import API_BASE_URL from '@/config';

export default {
  components: { ProductList, BasePagination, ProductFilter },
  data() {
    return {
      filterPriceFrom: 0,
      filterPriceTo: 0,
      filterCategoryId: 0,
      filterColorIds: [],
      filterMaterialIds: [],
      filterSeasonIds: [],
      page: 1,
      productsPerPage: 12,
      productsData: null,
      productsLoading: false,
      productsLoadingFailed: false,
    };
  },
  computed: {
    products() {
      let prods = [];
      if (this.productsData) {
        prods = this.productsData.items.map((product) => ({
          ...product,
          image: ((product.colors[0].gallery) ? product.colors[0].gallery[0].file.url : '/img/no-photo.svg'),
        }));
      } else {
        prods = [];
      }
      return prods;
    },
    countProducts() {
      return this.productsData ? this.productsData.pagination.total : 0;
    },
  },
  methods: {
    loadProducts() {
      this.productsLoading = true;
      this.productsLoadingFailed = false;
      clearTimeout(this.loadProductsTimer);
      this.loadProductsTimer = setTimeout(() => {
        axios
          .get(API_BASE_URL.concat('/api/products'), {
            params: {
              categoryId: this.filterCategoryId,
              materialIds: this.filterMaterialIds,
              seasonIds: this.filterSeasonIds,
              colorIds: this.filterColorIds,
              page: this.page,
              limit: this.productsPerPage,
              minPrice: this.filterPriceFrom,
              maxPrice: this.filterPriceTo,
            },
          })
          .then((response) => { this.productsData = response.data; })
          .catch(() => { this.productsLoadingFailed = true; })
          .then(() => { this.productsLoading = false; });
      }, 3000);
    },
  },
  watch: {
    page() {
      this.loadProducts();
    },
    productsPerPage() {
      this.loadProducts();
    },
    filterCategoryId() {
      this.loadProducts();
    },
    filterColorIds() {
      this.loadProducts();
    },
    filterMaterialIds() {
      this.loadProducts();
    },
    filterSeasonIds() {
      this.loadProducts();
    },
    filterPriceFrom() {
      this.loadProducts();
    },
    filterPriceTo() {
      this.loadProducts();
    },
  },
  created() {
    this.loadProducts();
  },
};
</script>

<style>
.windows8 {
  position: relative;
  width: 78px;
  height: 78px;
  margin: auto;
}
.windows8 .wBall {
  position: absolute;
  width: 74px;
  height: 74px;
  opacity: 0;
  transform: rotate(225deg);
  animation: orbit 6.96s infinite;
}
.windows8 .wBall .wInnerBall {
  position: absolute;
  width: 10px;
  height: 10px;
  background: rgb(0, 0, 0);
  left: 0px;
  top: 0px;
  border-radius: 10px;
}
.windows8 #wBall_1 {
  animation-delay: 1.52s;
}
.windows8 #wBall_2 {
  animation-delay: 0.3s;
}
.windows8 #wBall_3 {
  animation-delay: 0.61s;
}
.windows8 #wBall_4 {
  animation-delay: 0.91s;
}
.windows8 #wBall_5 {
  animation-delay: 1.22s;
}
@keyframes orbit {
  0% {
    opacity: 1;
    z-index: 99;
    transform: rotate(180deg);
    animation-timing-function: ease-out;
  }
  7% {
    opacity: 1;
    transform: rotate(300deg);
    animation-timing-function: linear;
    origin: 0%;
  }
  30% {
    opacity: 1;
    transform: rotate(410deg);
    animation-timing-function: ease-in-out;
    origin: 7%;
  }
  39% {
    opacity: 1;
    transform: rotate(645deg);
    animation-timing-function: linear;
    origin: 30%;
  }
  70% {
    opacity: 1;
    transform: rotate(770deg);
    animation-timing-function: ease-out;
    origin: 39%;
  }
  75% {
    opacity: 1;
    transform: rotate(900deg);
    animation-timing-function: ease-out;
    origin: 70%;
  }
  76% {
    opacity: 0;
    transform: rotate(900deg);
  }
  100% {
    opacity: 0;
    transform: rotate(900deg);
  }
}
</style>
