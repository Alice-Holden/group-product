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
          v-for="group in getRootGroups()"
          :key="group.id"
          title="Наведите, чтобы открыть подменю"
          class="product-menu__item d-flex"

      >
        <div
            class="d-flex"
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
            v-if="getSubGroups(group.id).length > 0"
            class="product-menu__submenu"
        >
          <div
              v-for="subgroup in getSubGroups(group.id)"
              :key="subgroup.id"
              class="product-menu__item"
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
            v-for="item in getItems(openProductsId)"
            :key="item.id"
            class="product-menu__product"
        >
          <div class="product-menu__title">{{item.product.name}}</div>
          <div v-if="item.product.price > 0">Цена: {{(item.product.price)/100}} руб.</div>
        </div>
      </div>
    </div>
    <div>
      <div
          v-if="showFoundProducts.length > 0"
          class="product-menu__products"
      >
        <div
            v-for="item in showFoundProducts"
            :key="item.id"
            class="product-menu__product"
        >
          <div class="product-menu__title">{{item.product.name}}</div>
          <div v-if="item.product.price > 0">Цена: {{(item.product.price)/100}} руб.</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * { "groups": [ { "id": 1, "idParent": null, "name": "Бытовая техника", "logo": "адрес до логотипа, может быть как локальный с диска, так и получаемый по сети" }, { "id": 2, "idParent": 1, "name": "Техника для кухни" }, { "id": 3, "idParent": 1, "name": "Техника для дома" }, { "id": 4, "idParent": null, "name": "Красота и здоровье" } ], "items": [ { "id": 109, "idGroup": 4, "product": { "id": 3, "name": "Шампунь Чистая линия", "price": 32000, "description": "Сделает ваши волосы неотразимыми", "weight": 1, "unitType": "л." } }, { "id": 100, "idGroup": 3, "product": { "id": 1, "name": "Утюг", "price": 190000 } }, { "id": 101, "idGroup": null, "product": { "id": 1, "name": "Прочий товар, без каталога", "price": 14600 } } ] }
 */
import struct from '../struct.json';

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
  methods:{
    getRootGroups(){
      return [...this.struct.groups.filter(group => group.idParent === null), {id: null, "idParent": null,  name: 'Прочие товары'}]
    },
    getSubGroups(idParent){
      if (idParent === null){
        return [];
      }
      return this.struct.groups.filter(group => group.idParent === idParent)
    },
    getItems(idGroup){
      return this.struct.items.filter(item => item.idGroup === idGroup)
    },
    //показываем товары по id группы
    openProducts(idGroup){
      this.openProductsId = idGroup;
      this.openSubmenuId = 0;

    },
    //поиск товара по наименованию
    searchProduct(){
      let search = this.search.toLowerCase();
      this.showFoundProducts  = this.struct.items.filter(item => item.product.name.toLowerCase().includes(search));
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
    padding: 30px;
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