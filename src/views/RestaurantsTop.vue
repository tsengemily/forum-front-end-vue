<template>
  <div class="container py-5">
    <NavTabs />
    <h1 class="mt-5">
      人氣餐廳
    </h1>

    <hr>
    <div
      class="card mb-3"
      style="max-width: 540px;margin: auto;"
      v-for="restaurant in restaurants"
      :key="restaurant.id"
    >
      <div class="row no-gutters">
        <div class="col-md-4">
          <router-link 
          :to="{name: 'restaurant', params: {id: restaurant.id}}"
          >
           <img
              class="card-img"
              :src="restaurant.image | emptyImage"
            >
          </router-link>
        </div>
        <div class="col-md-8">
          <div class="card-body">
            <h5 class="card-title">
              {{restaurant.name}}
            </h5>
            <span class="badge badge-secondary">收藏數：{{restaurant.FavoriteCount}}</span>
            <p class="card-text">
             {{restaurant.description}}
            </p>
            <router-link 
            class="btn btn-primary mr-2"
            :to="{name: 'restaurant', params: {id: restaurant.id}}"
            >
              Show
            </router-link>
            <button
              type="button"
              class="btn btn-danger mr-2"
              v-if="restaurant.isFavorited"
              @click.stop.prevent="removeFavorite(restaurant.id)"
            >
              移除最愛
            </button>
            <button
              type="button"
              class="btn btn-primary"
              v-else
              @click.stop.prevent="addFavorite(restaurant.id)"
            >
              加到最愛
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import NavTabs from '../components/NavTabs'
import { emptyImageFilter } from '../utils/mixins'
import restaurantsAPI from '../apis/restaurants'
import usersAPI from '../apis/users'
import { Toast } from '../utils/helpers'



export default {
  components: {
    NavTabs
  },
  data () {
    return {
      restaurants: [] 
    }
  },
  mixins: [emptyImageFilter],
  methods: {
    async fetchRestaurant () {
      try {
        const {data} = await restaurantsAPI.getTops() 
        this.restaurants = data.restaurants
      } catch (error) {
        console.log(error)
        Toast.fire({
          icon: 'error',
          title: '存取資料失敗，請稍後再試'
        })
      }
    },
    async addFavorite (restaurantId) {
      try {
        const {data} = await usersAPI.addFavorite({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurants = this.restaurants.map(restaurant => {
          if (restaurant.id !== restaurantId) {
            return restaurant
          } else {
            return {
              ...restaurant,
              FavoriteCount: restaurant.FavoriteCount + 1,
              isFavorited: true
            }
          }
        })
      } catch (error) {
        console.log(error)
        Toast.fire({
          icon: 'error',
          title: '無法加到最愛，請稍後再試'
        })
      }
    },
    async removeFavorite (restaurantId) {
      try {
        const {data} = await usersAPI.deleteFavorite({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurants = this.restaurants.map(restaurant => {
          if (restaurant.id !== restaurantId) {
            return restaurant
          } else {
            return {
              ...restaurant,
              FavoriteCount: restaurant.FavoriteCount - 1,
              isFavorited: false
            }
          }
        })
      } catch (error) {
        console.log(error)
        Toast.fire({
          icon: 'error',
          title: '無法加到最愛，請稍後再試'
        })
      }
    }
  },
  created() {
    this.fetchRestaurant() 
  }
}
</script>