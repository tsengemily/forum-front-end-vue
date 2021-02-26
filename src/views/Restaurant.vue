<template>
  <div class="container py-5">
    <RestaurantDetail 
    :init-restaurant="restaurant"
    />
    <hr>
    <RestaurantComments :restaurant-comments="restaurantComments"
    @after-delete-comment="afterDeleteComment"
    />
    <CreateComment 
    :restaurant-id="restaurant.id"
    @after-create-comment="afterCreateComment"
    />
  </div>
</template>

<script>
import RestaurantDetail from '../components/RestaurantDetail'
import RestaurantComments from '../components/RestaurantComments'
import CreateComment from '../components/CreateComment'
import restaurantsAPI from '../apis/restaurants'
import { Toast } from '../utils/helpers'
import { mapState } from 'vuex'


export default {
  name: 'Restaurant',
  components: {
    RestaurantDetail,
    RestaurantComments,
    CreateComment
  },
  data () {
    return {
      restaurant: {
        id: -1,
        name: '',
        categoryName: '',
        image: '',
        openingHours: '',
        tel: '',
        address: '',
        description: '',
        isFavorited: false,
        isLiked: false
      },
      restaurantComments: []
    }
  },
  computed: {
    ...mapState(['currentUser'])
  },
  methods: {
    async fetchRestaurant (restaurantId) {
      try {
        const { data } = await restaurantsAPI.getRestaurant({ restaurantId })
        const {restaurant, isFavorited, isLiked} = data
        const {id, name, Category, image, opening_hours, tel, address, description, Comments} = restaurant

        this.restaurant = {
          id,
          name, 
          categoryName: Category ? Category.name : '未分類',
          image,
          openingHours: opening_hours,
          tel,
          address,
          description,
          isFavorited,
          isLiked
        }
        this.restaurantComments = Comments 
      } catch (error) {
        console.log(error)
        Toast.fire({
          icon: 'error',
          title: '無法取得資料，請稍後再試'
        })
      }
    },
    afterDeleteComment (commentId) {
      this.restaurantComments = this.restaurantComments.filter(comment => comment.id !== commentId)
    },
    afterCreateComment (payload) {
      const {commentId, restaurantId, text} = payload
      this.restaurantComments.push({
        id: commentId,
        RestaurantId: restaurantId,
        User: {
          id: this.currentUser.id,
          name: this.currentUser.name
        },
        text,
        createdAt: new Date()
      })
    }
  },
  created () {
    const { id: restaurantId } = this.$route.params
    this.fetchRestaurant(restaurantId)
  },
  beforeRouteUpdate (to, next) {
    const { id: restaurantId } = to.params
    this.fetchRestaurant(restaurantId)
    next()
  }
}
</script>