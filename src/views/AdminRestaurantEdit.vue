<template>
  <div class="container py-5">
    <AdminRestaurantForm 
      :initial-restaurant="restaurant"
      @after-submit="handleAfterSubmit"
      :is-processing="isProcessing"
    />
  </div>
</template>

<script>
import AdminRestaurantForm from '../components/AdminRestaurantForm'
import adminAPI from '../apis/admin'
import { Toast } from '../utils/helpers'


export default {
  name: 'AdminRestaurantEdit',
  components: {
    AdminRestaurantForm
  },
  data () {
    return {
      restaurant: {
        id: -1,
        name: '',
        tel: '',
        address: '',
        openingHours: '',
        description: '',
        image: '',
        categoryId: ''
      },
      isProcessing: false
    }
  },
  methods: {
    async fetchRestaurant (restaurantId) {
      try {
        const { data } = await adminAPI.restaurants.getDetail({ restaurantId })
        const {
        id,
        name,
        tel,
        address,
        opening_hours: openingHours,
        description,
        image,
        CategoryId: categoryId
      } = data.restaurant
      this.restaurant = {
        ...this.restaurant,
        id,
        name,
        tel,
        address,
        openingHours,
        description,
        image,
        categoryId
      }
      } catch (error) {
        console.log(error)
        Toast.fire({
          icon: 'error',
          title: '資料取得失敗，請稍後再試'
        })
      }
    },
    async handleAfterSubmit (formData) {
      try {
        this.isProcessing = true
        const { data } = await adminAPI.restaurants.update({
          restaurantId: this.restaurant.id,
          formData
        })
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.$router.push({ name: 'admin-restaurants' })
      } catch (error) {
        console.log(error)
        this.isProcessing = false
        Toast.fire({
          icon: 'error',
          title: '無法更新資料，請稍後再試'
        })
      }
    }
  },
  created () {
    const {id} = this.$route.params 
    console.log(id)
    this.fetchRestaurant(id)
  },
  beforeRouteUpdate (to, next) {
    const { id } = to.params
    this.fetchRestaurant(id)
    next()
  }
}
</script>