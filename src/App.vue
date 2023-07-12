<template>
  <div class="page">
    <div class="page__title">Тестовое задание на vue 3</div>
    <div class="page__author">Задворная Наталья</div>
    <div>
      <ProductGroupMenu/>
    </div>
  </div>

</template>

<script>

import ProductGroupMenu from "@/components/ProductGroupMenu.vue";

export default {
  name: 'App',
  components: {
    ProductGroupMenu
  },
  data() {
    return {

    }
  },
  mounted() {
    setInterval(this.generateBlueColor, 3000)
  },

  methods: {
    /**
     * Generates color in RGB format what looks like blue color
     */
    generateBlueColor(){
      let h = Math.floor(Math.random() * (160 - 200 + 1)) + 200;
      let s = Math.floor(Math.random() * (50 - 100 + 1)) + 100;
      let l = Math.floor(Math.random() * (20 - 80 + 1)) + 80;
      document.querySelector('.page__title').style.background = `hsl(${h}, ${s}%, ${l}%, .5)`
    }

  }

}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
div{
  box-sizing: border-box;
}
body{
  margin: 0;
  padding: 0;
  background: whitesmoke;
}
.page{
  &__title{
    transition: all 1s ease-in-out;
    width: 100%;
    padding: 20px;
    font-size: 16px;
    color: white;
    background: #b1ecec;
  }
  &__author{
    width: 100%;
    padding: 5px;
    font-size: 16px;
    text-align: right;
  }
}

</style>
