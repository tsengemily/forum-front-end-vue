<template>
  <div class="col-md-6 col-lg-4">
    <div class="card mb-4">
      <img
        class="card-img-top"
        :src="restaurant.image | emptyImage"
        alt="Card image cap"
        width="286px"
        height="180px"
      >
      <div class="card-body">
        <p class="card-text title-wrap">
          <router-link 
          :to="{name: 'restaurant', params: {id: restaurant.id}}"
          >
          {{restaurant.name}}
          </router-link>
        </p>
        <span class="badge badge-secondary">{{restaurant.Category ? restaurant.Category.name : '未分類'}}</span>
        <p class="card-text text-truncate">
          {{restaurant.description}}
        </p>
      </div>
      <div class="card-footer">
        <button
          v-if="restaurant.isFavorited"
          @click.stop.prevent="deleteFavorite(restaurant.id)"
          type="button"
          class="btn btn-danger btn-border favorite mr-2"
        >
          移除最愛
        </button>
        <button
          v-else
          @click.stop.prevent="addFavorite(restaurant.id)"
          type="button"
          class="btn btn-primary btn-border favorite mr-2"
        >
          加到最愛
        </button>
        <button
          v-if="restaurant.isLiked"
          @click.stop.prevent="deleteLike(restaurant.id)"
          type="button"
          class="btn btn-danger like mr-2"
        >
          Unlike
        </button>
        <button
          v-else
          @click.stop.prevent="addLike(restaurant.id)"
          type="button"
          class="btn btn-primary like mr-2"
        >
          Like
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { emptyImageFilter } from '../utils/mixins'
import usersAPI from '../apis/users'
import { Toast } from '../utils/helpers'

export default {
  props: {
    initRestaurant: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      restaurant: this.initRestaurant
    }
  },
  mixins: [emptyImageFilter],
  methods: {
    async addFavorite (restaurantId) {
      try {
        const {data} = await usersAPI.addFavorite({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurant = {
        ...this.restaurant,
        isFavorited: true
       }
      } catch (error) {
        Toast.fire({
          icon: 'error',
          title: '無法將餐廳加入最愛，請稍後再試'
        })
        console.log(error)
      }
    },
    async deleteFavorite (restaurantId) {
      try {
        const {data} = await usersAPI.deleteFavorite({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurant = {
        ...this.restaurant,
        isFavorited: false
       }
      } catch (error) {
        Toast.fire({
          icon: 'error',
          title: '無法將餐廳刪除最愛，請稍後再試'
        })
        console.log(error)
      }
    },
    async addLike (restaurantId) {
      try {
        const {data} = await usersAPI.addLike({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurant = {
        ...this.restaurant,
        isLiked: true
       }
      } catch (error) {
        Toast.fire({
          icon: 'error',
          title: '無法將餐廳加入喜歡，請稍後再試'
        })
      }
    },
    async deleteLike (restaurantId) {
      try {
        const {data} = await usersAPI.deleteLike({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurant = {
        ...this.restaurant,
        isLiked: false
       }
      } catch (error) {
        Toast.fire({
          icon: 'error',
          title: '無法將餐廳刪除喜歡，請稍後再試'
        })
      }
    }
  }
}
</script>