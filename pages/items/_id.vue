<template>
  <main class="container">
    <section
      class="image"
      :style="`background: url(/${currentItem.img}) center center no-repeat`"
    ></section>

    <section class="options">
      <h1>{{ currentItem.item }}</h1>
      <h3>Price: {{ priceFormatting(currentItem.price) }}</h3>

      <div class="quantity">
        <input type="number" min="1" v-model="itemQuantity" />
        <button class="primary" @click="addToCart">
          Add to Cart - {{ priceFormatting(totalPrice) }}
        </button>
      </div>

      <fieldset v-if="currentItem.options">
        <legend><h3>Options</h3></legend>
        <div v-for="option in currentItem.options" :key="option">
          <input
            type="radio"
            name="option"
            :id="option"
            :value="option"
            :key="option"
            v-model="itemOption"
          />
          <label :for="option">{{ option }}</label>
        </div>
      </fieldset>
      <fieldset v-if="currentItem.addOns">
        <legend><h3>Add Ons</h3></legend>
        <div v-for="addOn in currentItem.addOns" :key="addOn">
          <input
            type="checkbox"
            name="addOn"
            :id="addOn"
            :value="addOn"
            :key="addOn"
            v-model="itemAddOns"
          />
          <label :for="addOn">{{ addOn }}</label>
        </div>
      </fieldset>

      <AppToast v-if="cartSubmitted"
        >Order submitted<br />
        Check out more
        <nuxt-link to="/restaurants">restaurants</nuxt-link></AppToast
      >
    </section>

    <section class="details" v-if="currentItem.hasOwnProperty(`description`)">
      <h3>Description</h3>
      <p>{{ currentItem.description }}</p>
    </section>
  </main>
</template>

<script>
import { mapState } from "vuex";
import AppToast from "@/components/AppToast.vue";

export default {
  components: {
    AppToast
  },
  data() {
    return {
      id: this.$route.params.id,
      itemQuantity: 1,
      itemOption: "",
      itemAddOns: [],
      itemSizeAndCost: [],
      cartSubmitted: false
    };
  },
  computed: {
    ...mapState(["fooddata"]),
    currentItem() {
      let result;

      for (let i = 0; i < this.fooddata.length; i++) {
        for (let j = 0; j < this.fooddata[i].menu.length; j++) {
          if (this.fooddata[i].menu[j].id === this.id) {
            result = this.fooddata[i].menu[j];
            break;
          }
        }
      }

      return result;
    },
    totalPrice() {
      return this.itemQuantity * this.currentItem.price;
    }
  },
  methods: {
    priceFormatting(item) {
      return `$${item.toFixed(2)}`;
    },
    addToCart() {
      let formOutput = {
        item: this.currentItem.item,
        quantity: this.itemQuantity,
        option: this.itemOption,
        addOns: this.itemAddOns,
        totalPrice: this.totalPrice
      };
      this.cartSubmitted = true;
      this.$store.commit("addToCart", formOutput);
    }
  }
};
</script>

<style lang="scss" scoped>
.container {
  width: 1000px;
  margin: 100px auto;
  display: grid;
  grid-template-columns: 400px 1fr;
  grid-template-rows: 400px 1fr;
  grid-column-gap: 60px;
  grid-row-gap: 60px;
  line-height: 2;
}

.image {
  grid-area: 1/1/2/2;
}

.options {
  grid-area: 1/2/3/3;
  position: relative;
}

.details {
  grid-area: 2/1/3/2;
}
</style>
