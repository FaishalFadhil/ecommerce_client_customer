<template>
  <div class="column is-3">
    <lottie-player src="https://assets1.lottiefiles.com/packages/lf20_hluo7ags.json"  background="transparent"  speed="1"  style="width: 300px; height: 300px;"  loop autoplay v-if="isLoading === true"/>
    <div class="card" v-else>
      <div class="card-image">
        <figure class="image is-3by2">
          <img :src="list.Product.image_url" alt="Placeholder image">
        </figure>
      </div>
      <div class="card-content">
        <div class="media">
          <div class="media-content">
            <p class="title is-7">{{list.Product.name}}</p>
            <p class="subtitle is-7">{{list.Product.category}}</p>
          </div>
        </div>

        <div class="content" style="list-style-type: none">
          <li class="is-size-7"><b>Price: </b>{{priceRp}}</li>
        </div>
        <div class="buttons">
          <a @click.prevent="addToCart(list.id)" class="button is-small" v-if="list.Product.stock > 0">
            <span class="icon is-small">
              <i class="fas fa-shopping-cart"></i>
            </span>
          </a>
          <a class="button is-small" @click.prevent="deleteWishlist(list.id)">
                <span class="icon is-small">
                  <i class="fas fa-trash"></i>
                </span>
              </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductCard',
  data () {
    return {
      isLoading: false
    }
  },
  props: ['list'],
  methods: {
    addToCart (id) {
      this.isLoading = true
      this.$store.dispatch('addCart', id)
        .then(res => {
          this.$swal.fire({
            title: 'Item added to cart!',
            text: 'You can add more or just check you cart',
            icon: 'success'
          })
        })
        .catch(err => {
          this.$swal.fire({
            icon: 'error',
            title: `${err.response.status} ${err.response.statusText}`,
            text: `${err.response.data.message}`,
            timer: 5000
          })
        })
        .finally(() => {
          this.isLoading = false
        })
    },
    deleteWishlist (id) {
      this.$swal.fire({
        title: 'Are you sure to remove?',
        text: "Maybe it's not your wish right?",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#d33',
        confirmButtonText: 'Yes'
      })
        .then((result) => {
          if (result.isConfirmed) {
            this.isLoading = true
            return this.$store.dispatch('deleteWishlist', id)
          }
        })
        .then(res => {
          return this.$store.dispatch('fetchWishlists')
        })
        .then(res => {
          this.$swal.fire({
            title: 'Deleted',
            text: "It's already deleted, let's find another wish!",
            icon: 'success'
          })
          this.$store.commit('FETCH_WISHLISTS', res.data)
        })
        .catch(err => {
          this.$swal.fire({
            icon: 'error',
            title: `${err.response.status} ${err.response.statusText}`,
            text: `${err.response.data.message}`,
            timer: 5000
          })
        })
        .finally(() => {
          this.isLoading = false
        })
    }
  },
  computed: {
    priceRp: function () {
      let rupiah = ''
      const priceReverse = this.list.Product.price.toString().split('').reverse().join('')
      for (let i = 0; i < priceReverse.length; i++) if (i % 3 === 0) rupiah += priceReverse.substr(i, 3) + '.'
      return 'Rp. ' + rupiah.split('', rupiah.length - 1).reverse().join('')
    }
  }
}
</script>

<style>

</style>
