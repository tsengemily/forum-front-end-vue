<template>
  <div class="container py-5">
    <NavTabs />
     <RestaurantsNavPills :categories="categories"/>

    <div class="row">
      <RestaurantCard :initRestaurant="restaurant" v-for="restaurant in restaurants" :key="restaurant.id"/>
    </div>

    <RestaurantsPagination 
    v-if="totalPage.length > 1"
    :current-page="currentPage"
    :total-page="totalPage"
    :previous-page="previousPage"
    :next-page="nextPage"
    :category-id="categoryId"
    />
  </div>
</template>

<script>
import NavTabs from '../components/NavTabs'
import RestaurantCard from '../components/RestaurantCard'
import RestaurantsNavPills from '../components/RestaurantsNavPills'
import RestaurantsPagination from '../components/RestaurantsPagination'
import restaurantsAPI from '../apis/restaurants'
import {Toast} from '../utils/helpers'


export default {
  name: 'Restaurants',
  components: {
    NavTabs,
    RestaurantCard,
    RestaurantsNavPills,
    RestaurantsPagination
  },
  data () {
    return {
      restaurants: [],
      categories: [],
      categoryId: -1,
      currentPage: 1,
      totalPage: [],
      previousPage: -1,
      nextPage: -1
    }
  },
  created () {
    const { page = '', categoryId = '' } = this.$route.query
    this.fetchRestaurants({
      queryPage: page, 
      queryCategoryId: categoryId
    })
  },
  beforeRouteUpdate (to, from, next) {
    const {page = '', categoryId = ''} = to.query
    this.fetchRestaurants({ 
        queryPage: page, 
        queryCategoryId: categoryId
    })
    next()
  },
  methods: {
      async fetchRestaurants ({queryPage, queryCategoryId}) {
          try {
              const response = await restaurantsAPI.getRestaurants({
                  page: queryPage,
                  categoryId: queryCategoryId
              })
                const {
                    restaurants, 
                    categories, 
                    categoryId, 
                    page, 
                    totalPage, 
                    prev, 
                    next
                    } = response.data
                this.restaurants = restaurants
                this.categories = categories
                this.categoryId = categoryId
                this.currentPage = page
                this.totalPage = totalPage
                this.previousPage = prev
                this.nextPage = next
          } catch (error) {
              Toast.fire({
                  icon: 'error',
                  title: '無法取得資料，請確認連線'
              })
              console.log (error)
          } 
      }
  }
}
</script>