<script>
export default {
  props: { lessons: Array },
  methods: {
    handleAddItemToCart(lesson) {
      this.$emit("add-item-to-cart", lesson);
    },
    getProdImageURL(imgURL) {
      return "https://lessons-app-assignment.herokuapp.com" + imgURL;
    },
  },
};
</script>

<template>
  <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 g-3">
    <div
      class="col-12 col-sm-6 col-md-6"
      style="margin-bottom: 40px"
      v-for="lesson in lessons"
      :key="lesson.id"
    >
      <div class="card shadow-sm">
        <img
          class="card-img-top prod-img"
          :src="getProdImageURL(lesson.image)"
          alt="Image"
        />

        <div class="card-body">
          <i :class="`my-icon fa ${lesson.icon}`"></i>
          <h4>{{ lesson.subject }}</h4>
          <h6 class="card-title">Location: {{ lesson.location }}</h6>

          <div class="card-text mt-2">
            <span class="font-weight-bold float-left">
              <span>${{ lesson.price }}</span>
              <br />
              <span
                class="badge badge-pill badge-primary"
                v-if="lesson.space > 0"
              >
                {{ lesson.space }} Spaces
              </span>
            </span>
            <button
              :disabled="lesson.space == 0"
              class="btn mt-2 btn-sm float-right text-uppercase"
              @click="handleAddItemToCart(lesson)"
            >
              <i class="fas fa-cart-plus fa-2x green"></i>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.prod-img {
  height: 200px;
}
</style>