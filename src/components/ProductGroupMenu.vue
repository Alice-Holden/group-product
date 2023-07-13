<template>
  <div
      class="input-group mb-3"
      @keydown.enter="searchProduct"
  >
    <input
        v-model="search"
        type="search"
        class="form-control"
        placeholder="Поиск товара"
        aria-label="Поиск товара по наименованию"
    />
    <div class="input-group-append">
      <button
          class="btn btn-outline-secondary"
          type="button"
          @click="searchProduct"
      >
        Поиск
      </button>
    </div>
  </div>
  <div>
    <div class="product-menu">
      <div
          v-for="group in groups"
          :key="group.id"
          title="Наведите, чтобы открыть подменю"
          class="product-menu__item d-flex"

      >
        <div
            class="d-flex"
            style="padding: 30px"
            @click="openProducts(group.id)"
        >
          <div
              v-if="group.logo"
              class="product-menu__logo"
          >
            <img
                :src="group.logo"
                alt="logo"
            />
          </div>
          <div>{{group.name}}</div>
        </div>
        <div
            v-if="group.getSubGroups().length > 0"
            class="product-menu__submenu"
        >
          <div
              v-for="subgroup in group.getSubGroups()"
              :key="subgroup.id"
              class="product-menu__item"
              style="padding: 30px"
              title="Нажмите, чтобы открыть товары"
              @click="openProducts(subgroup.id)"
          >
           {{subgroup.name}}
          </div>
        </div>
      </div>
    </div>
    <div v-if="showFoundProducts.length === 0">
      <div
          v-if="openProductsId !== 0"
          class="product-menu__products"
      >
        <div
            v-for="product in getProductsToDisplay(openProductsId)"
            :key="product.id"
            class="product-menu__product"
        >
          <div class="product-menu__title">{{product.info.name}}</div>
          <div v-if="product.info.price > 0">Цена: {{(product.info.price)/100}} руб.</div>
        </div>
      </div>
    </div>
    <div>
      <div
          v-if="showFoundProducts.length > 0"
          class="product-menu__products"
      >
        <div
            v-for="product in showFoundProducts"
            :key="product.id"
            class="product-menu__product"
        >
          <div class="product-menu__title">{{product.info.name}}</div>
          <div v-if="product.info.price > 0">Цена: {{(product.info.price)/100}} руб.</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import struct from '../struct.json';

// Define class Group what contains group attributes and have getSubGroups and getProducts methods
class Group {
  constructor(group) {
    this.id = group.id;
    this.idParent = group.idParent;
    this.name = group.name;
    this.logo = group.logo;
  }

  getSubGroups() {
    return struct.groups.filter(group => group.idParent === this.id);
  }

  getProducts() {
    return struct.items.filter(item => item.idGroup === this.id).map(item => new Product(item));
  }
}

class Product {
  constructor(item) {
    this.id = item.id;
    this.idGroup = item.idGroup;
    this.info = item.product;
  }
}

export default {
  name: "ProductGroupMenu",
  data() {
    return {
      struct: struct,
      //тк null используется в объекте, то для закрытия подменю по умолчанию используем 0
      openSubmenuId: 0,
      openProductsId: 0,
      search: '',
      showFoundProducts: []
    };
  },
  computed: {
    groups() {
      return this.struct.groups.map(group => new Group(group));
    },
  },
  methods:{
    getProductsToDisplay(idGroup){
      const group = this.struct.groups.find(group => group.id === idGroup);
      if (group === undefined){
        return [];
      }

      return new Group(group).getProducts();
    },
    //показываем товары по id группы
    openProducts(idGroup){
      this.openProductsId = idGroup;
      this.openSubmenuId = 0;

    },
    //поиск товара по наименованию
    searchProduct(){
      let search = this.search.toLowerCase();
      this.showFoundProducts  = this.struct.items
          .filter(item => item.product.name.toLowerCase().includes(search))
          .map(item => new Product(item));
    }
  }
}
</script>

<style scoped lang="scss">
.product-menu{
  display: flex;
  flex-direction: row;
  margin: 10px;
  align-items: center;
  flex-wrap: wrap;
  border: 1px solid #066786;
  border-radius: 4px;


  &__item{
    position: relative;
    cursor: pointer;
    color: #066786;
    transition: all .3s ease-in-out;
    &:hover{
      background: #c9efef;
    }
    &:hover .product-menu__submenu{
      display: block;
    }
  }
  &__submenu{
    display: none;
    position: absolute;
    width: 200px;
    border: 1px solid #066786;
    border-radius: 4px;
    background: whitesmoke;
    cursor: pointer;
    top: 80%;
    overflow: hidden;
    z-index: 2;
  }
  &__logo{
    width: 24px;
    height: 24px;
    margin-right: 4px;
    img{
      width: 100%;
    }
  }
  &__products{
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    gap: 30px;
    margin: 10px;
  }
  &__product{
    padding: 30px;
    border: 1px solid #066786;
    border-radius: 4px;
    width: 300px;
    background: #e4f6f6;
    color: #066786;
    transition: all .3s ease-in-out;
  }
  &__title{
    font-weight: bold;
    font-size: 16px;
  }
}
.input-group{
  min-width: 200px;
  max-width: 500px;
  margin: 10px;
}
</style>