<!-- eslint-disable max-len -->
<template>
  <main class="content container" v-if="cartProductsLoading"><h1>
    Загрузка товаров из корзины...</h1></main>
  <main class="content container" v-else-if="cartProductsLoadingFailed"><h1>
    Не удалось загрузить товары из корзины!</h1></main>
  <main class="content container" v-else>
    <div class="content__top">
      <ul class="breadcrumbs">
        <li class="breadcrumbs__item">
          <router-link class="breadcrumbs__link" :to="{ name: 'main' }">
            Каталог
          </router-link>
        </li>
        <li class="breadcrumbs__item">
          <a class="breadcrumbs__link">
            Корзина
          </a>
        </li>
      </ul>

      <div class="content__row">
        <h1 class="content__title">
          Корзина
        </h1>
        <span class="content__info">
          {{ products.length }} товара(ов)
        </span>
      </div>
    </div>

    <section class="cart">
      <form class="cart__form form" action="#" method="POST">
        <div class="cart__field">
          <ul class="cart__list">
            <CartItem v-for="item in products" :key="item.id" :item="item" />
          </ul>
        </div>

        <div class="cart__block">
          <p class="cart__desc">
            Мы&nbsp;посчитаем стоимость доставки на&nbsp;следующем этапе
          </p>
          <p class="cart__price">
            Итого: <span>{{ totalPrice | numberFormat }} ₽</span>
          </p>

          <router-link tag="button" :to="{name: 'order'}" v-show="totalPrice > 0"
            class="cart__button button button--primery" type="submit">
            Оформить заказ
          </router-link>
        </div>
      </form>
    </section>
  </main>
</template>

<script>
import numberFormat from '@/helpers/numberFormat';
import { mapGetters } from 'vuex';
import CartItem from '@/components/CartItem.vue';

export default {
  components: { CartItem },
  filters: { numberFormat },
  computed: {
    ...mapGetters({
      // products: 'cartDetailProducts',
      products: 'getCartProducts',
      totalPrice: 'cartTotalPrice',
      cartProductsLoading: 'getCartProductsLoading',
      cartProductsLoadingFailed: 'getCartProductsLoadingFailed',
    }),
  },
};
</script>
