<script>
export default {
  props: {
    showCart: Boolean,
    cartItems: Array,
  },
  data() {
    return {
      doCheckout: false,
      checkoutName: "",
      checkoutPhone: "",
      isValidName: false,
      isValidPhone: false,
    };
  },
  methods: {
    removeItem(item) {
      this.$emit("remove-item", item.id);
    },
    checkName() {
      this.isValidName = /^[a-zA-Z]+$/.test(this.checkoutName) ? true : false;
    },
    checkPhone() {
      this.isValidPhone = /^[0-9]+$/.test(this.checkoutPhone) ? true : false;
    },
    onCheckout() {
      this.$emit("on-checkout");
    },
    onToggleShowCart() {
      this.$emit("toggle-showcart");
    },
    getProdImageURL(imgURL) {
      return "https://lessons-app-assignment.herokuapp.com" + imgURL;
    },
  },
};
</script>

<template>
  <div class="cart col-12">
    <div>
      <div>
        <div>
          <div>
            <button class="btn mr-2 btn-primary" @click="onToggleShowCart">
              <i class="fa fa-chevron-left"></i>
              Back
            </button>
            <h2 class="font-weight-bold mt-3">
              Cart - {{ cartItems.length }} items
            </h2>
          </div>
          <table class="table table-bordered">
            <thead>
              <tr>
                <th>#</th>
                <th>Image</th>
                <th>Subject</th>
                <th>Location</th>
                <th>Price</th>
                <th>Action</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in cartItems" :key="index">
                <td scope="row">{{ index + 1 }}</td>
                <td>
                  <img
                    class="mr-3 prod-img"
                    width="72px"
                    height="72px"
                    :src="getProdImageURL(item.image)"
                    alt="Generic placeholder image"
                  />
                </td>
                <td>{{ item.subject }}</td>
                <td>{{ item.location }}</td>
                <td>${{ item.price }}</td>
                <td>
                  <button
                    style="max-height: 42px !important"
                    class="btn text-danger"
                    @click="removeItem(item)"
                  >
                    <i class="fa fa-trash"></i>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div class="card-action">
        <a
          class="btn"
          style="background: #4ec690; color: white"
          href="#checkout"
          @click="doCheckout = true"
        >
          Proceed to checkout
        </a>
      </div>

      <!-- checkout -->

      <div class="card-text row" v-if="doCheckout">
        <form @submit.prevent="checkout" class="col-12 col-sm-8" id="checkout">
          <h2 class="font-weight-bold mt-4">Checkout</h2>
          <div class="form-group">
            <label>Name</label>
            <input
              type="text"
              v-model="checkoutName"
              @input="checkName"
              class="form-control"
              placeholder="Enter name"
            />
          </div>
          <div class="form-group">
            <label>Phone</label>
            <input
              type="number"
              v-model="checkoutPhone"
              @input="checkPhone"
              class="form-control"
              placeholder="Enter phone"
            />
          </div>
          <button
            class="btn bg-primary"
            v-if="isValidName && isValidPhone"
            @click="onCheckout"
          >
            Checkout
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
.prod-img {
  height: 80px;
  width: 80px;
}
</style>>
