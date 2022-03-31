<script >
import Lessons from "./components/Lessons.vue";
import Checkout from "./components/Checkout.vue";
import { lessons } from "./data/lessons";

export default {
  components: {
    lessons: Lessons,
    checkout: Checkout,
  },
  data() {
    return {
      lessons: [],
      showCart: false,
      cartItems: [],
      doCheckout: false,
      checkoutName: "",
      checkoutPhone: "",
      isValidName: false,
      isValidPhone: false,
      orderPlaced: false,
      parameters: ["subject", "location", "price", "availability"],
      sortBy: "",
      filteredLessons: [],
    };
  },
  mounted() {
    fetch("https://lessons-app-assignment.herokuapp.com/lessons")
      .then((res) => res.json())
      .then((data) => {
        const lessons = data.lessons;
        this.lessons = lessons;
        this.filteredLessons = lessons;
        console.log("data: ", lessons);
      });
  },
  methods: {
    addItemToCart(item) {
      if (item.space > 0) {
        let lesson = this.lessons.findIndex((obj) => obj.id == item.id);
        if (this.lessons[lesson]?.space > 0) {
          this.cartItems.push(item);
          this.lessons[lesson].space = this.lessons[lesson].space - 1;
          if (this.lessons[lesson].space == 0) {
            this.lessons[lesson].availability = 0;
          } else {
            this.lessons[lesson].availability = 1;
          }
        }
      }
    },
    checkName() {
      this.isValidName = /^[a-zA-Z]+$/.test(this.checkoutName) ? true : false;
    },
    checkPhone() {
      this.isValidPhone = /^[0-9]+$/.test(this.checkoutPhone) ? true : false;
    },
    onCheckout() {
      this.cartItems = [];
      this.orderPlaced = true;
      this.doCheckout = false;
    },
    sortLessons(sort) {
      if (sort == "desc") {
        this.filteredLessons.sort((a, b) => (a[this.sortBy] < b[this.sortBy] ? 1 : -1));
      } else {
        this.filteredLessons.sort((a, b) => (a[this.sortBy] > b[this.sortBy] ? 1 : -1));
      }
    },
    searchItem(event) {
      this.filteredLessons = this.lessons.filter((item) => {
        return (
          item.subject.toString().toLowerCase().includes(event.target.value) ||
          item.location.toString().toLowerCase().includes(event.target.value) ||
          item.subject.toString().includes(event.target.value) ||
          item.location.toString().includes(event.target.value)
        );
      });
      this.filteredLessons = this.filteredLessons.filter((item) =>
        Object.values(item).filter((value) =>
          value.toString().toLowerCase().includes(event.target.value)
        )
      );
    },
    removeItem(id) {
      console.log(id);
      let lessonIndex = this.lessons.findIndex((obj) => obj.id === id);
      let cartIndex = this.cartItems.findIndex((obj) => obj.id === id);
      this.cartItems.splice(cartIndex, 1);
      this.lessons[lessonIndex].space = this.lessons[lessonIndex].space + 1;
      this.lessons[lessonIndex].availability = 1;
      if (this.cartItems.length == 0) {
        this.showCart = false;
      }
    },
    toggleShowCart() {
      this.showCart = !this.showCart;
    },
  },
};
</script>

<template>
  <header>
    <div class="collapse bg-dark" id="navbarHeader">
      <div class="container"></div>
    </div>
    <div class="navbar navbar-dark bg-dark shadow-sm">
      <div class="container">
        <a href="#" class="navbar-brand align-items-center">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="35"
            height="35"
            fill="none"
            stroke="currentColor"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            aria-hidden="true"
            viewBox="0 0 24 24"
          >
            <path
              d="M1 2.828c.885-.37 2.154-.769 3.388-.893 1.33-.134 2.458.063 3.112.752v9.746c-.935-.53-2.12-.603-3.213-.493-1.18.12-2.37.461-3.287.811V2.828zm7.5-.141c.654-.689 1.782-.886 3.112-.752 1.234.124 2.503.523 3.388.893v9.923c-.918-.35-2.107-.692-3.287-.81-1.094-.111-2.278-.039-3.213.492V2.687zM8 1.783C7.015.936 5.587.81 4.287.94c-1.514.153-3.042.672-3.994 1.105A.5.5 0 0 0 0 2.5v11a.5.5 0 0 0 .707.455c.882-.4 2.303-.881 3.68-1.02 1.409-.142 2.59.087 3.223.877a.5.5 0 0 0 .78 0c.633-.79 1.814-1.019 3.222-.877 1.378.139 2.8.62 3.681 1.02A.5.5 0 0 0 16 13.5v-11a.5.5 0 0 0-.293-.455c-.952-.433-2.48-.952-3.994-1.105C10.413.809 8.985.936 8 1.783z"
            />
          </svg>
          <strong>Lessons</strong>
        </a>
        <span class="float-right btn-lg" @click="showCart = !showCart">
          <i class="fas fa-shopping-cart" style="color: white"></i>
          <span
            class="badge badge-primary badge-pill"
            style="
              position: absolute;
              top: 2px;
              border-radius: 50%;
              background: #4ec690;
            "
            >{{ cartItems.length }}</span
          >
        </span>
      </div>
    </div>
  </header>

  <main>
    <div class="album py-5 bg-light">
      <div class="container">
        <div
          class="alert alert-success alert-dismissible fade show"
          role="alert"
          v-if="orderPlaced"
        >
          <strong>Success</strong> Order placed successifully
          <button
            type="button"
            class="close"
            data-dismiss="alert"
            aria-label="Close"
            @click="orderPlaced = false"
          >
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div v-if="showCart && cartItems.length > 0">
          <checkout
            :showCart="showCart"
            :cartItems="cartItems"
            @remove-item="removeItem"
            @on-checkout="onCheckout"
            @toggle-showcart="toggleShowCart"
          ></checkout>
        </div>

        <div v-else>
          <div class="card mb-5">
            <h5 class="card-header bg-dark text-light">Sort & Filter</h5>
            <div class="card-body">
              <div class="form-group row">
                <div class="col-md-4">
                  <div class="input-group mb-3 mr-sm-4">
                    <input
                      type="search"
                      @input="searchItem($event)"
                      class="form-control"
                      placeholder="Search lesson..."
                      aria-label="Search lesson..."
                    />
                    <div class="input-group-append">
                      <span
                        style="background: #4ec690; color: white"
                        class="fa fa-search input-group-text pt-2"
                      ></span>
                    </div>
                  </div>
                </div>
                <div class="col-4">
                  <select
                    type="search"
                    aria-placeholder="Sort By"
                    v-model="sortBy"
                    @change="sortLessons('desc')"
                    class="form-control"
                  >
                    <option value="">Select Sort Option</option>
                    <option
                      v-for="(parameter, index) in parameters"
                      :key="index"
                      :value="parameter"
                      class="text-capitalize"
                    >
                      {{ parameter }}
                    </option>
                  </select>
                </div>
                <div class="col-4 px-1">
                  <button
                    class="btn btn-link"
                    style="color: white; background: #4ec690"
                    @click="sortLessons('asc')"
                  >
                    <i class="fa fa-chevron-circle-up"></i>
                  </button>
                  <button
                    class="btn btn-link"
                    style="color: white; background: #4ec690"
                    @click="sortLessons('desc')"
                  >
                    <i class="fa fa-chevron-circle-down"></i>
                  </button>
                </div>
              </div>
            </div>
          </div>
          <lessons
            :lessons="filteredLessons"
            @add-item-to-cart="addItemToCart"
          ></lessons>
        </div>
      </div>
    </div>
  </main>
</template>
