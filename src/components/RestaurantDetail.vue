<template>
  <div class="row">
    <div class="col-md-12 mb-3">
      <h1>{{restaurant.name}}</h1>
      <p class="badge badge-secondary mt-1 mb-3">
        {{restaurant.categoryName}}
      </p>
    </div>
    <div class="col-lg-4">
      <img
        class="img-responsive center-block" 
    :src="restaurant.image | emptyImage"
        style="width: 250px;margin-bottom: 25px;"
      >
      <div class="contact-info-wrap">
        <ul class="list-unstyled">
          <li>
            <strong>Opening Hour:</strong>
            {{restaurant.openingHours}}
          </li>
          <li>
            <strong>Tel:</strong>
            {{restaurant.tel}}
          </li>
          <li>
            <strong>Address:</strong>
            {{restaurant.address}}
          </li>
        </ul>
      </div>
    </div>
    <div class="col-lg-8">
      <p>{{restaurant.description}}</p>
      <router-link 
      class="btn btn-primary btn-border mr-2"
      :to="{name: 'restaurant-dashboard', params: {id: restaurant.id}}">
        Dashboard
      </router-link>

      <button
        type="button"
        class="btn btn-danger btn-border mr-2"
        @click="removeFavorite(restaurant.id)"
        v-if="restaurant.isFavorited"
      >
        移除最愛
      </button>
      <button
        type="button"
        class="btn btn-primary btn-border mr-2"
        @click="addFavorite(restaurant.id)"
        v-else
      >
        加到最愛
      </button>
      <button
        type="button"
        class="btn btn-danger like mr-2"
        @click="removeLike(restaurant.id)"
        v-if="restaurant.isLiked"
      >
        Unlike
      </button>
      <button
        type="button"
        class="btn btn-primary like mr-2"
        @click="addLike(restaurant.id)"
        v-else
      >
        Like
      </button>
    </div>
  </div>
</template>

<script>
import { emptyImageFilter } from '../utils/mixins'
import usersAPI from '../apis/users'
import { Toast } from '../utils/helpers'

export default {
  name: 'Detail',
  props: {
    initRestaurant: {
      type: Object,
      required: true
    }
  },
  mixins: [emptyImageFilter],
  data () {
    return {
      restaurant: this.initRestaurant
    }
  },
  watch: {
    initRestaurant (newValue) {
      this.restaurant = {
        ...this.restaurant,
        ...newValue
      }
    }
  },
  methods: {
    async addFavorite (restaurantId) {
      try {
        const { data } = await usersAPI.addFavorite({ restaurantId })
        
        if (data.status !== 'success') {
          throw new Error(data.message)
        }

         this.restaurant = {
          ...this.restaurant,
          isFavorited: true
        }
      } catch (error) {
        console.log('error', error)
        Toast.fire({
          icon: 'error',
          title: '加入失敗，請稍後再試'
        })
      }
    },
    async removeFavorite (restaurantId) {
      try {
        const { data } = await usersAPI.deleteFavorite({ restaurantId }) 

        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        
        this.restaurant = {
          ...this.restaurant,
          isFavorited: false
        }
      } catch (error) {
        console.log('error', error)
        Toast.fire({
          icon: 'error',
          title: '移除失敗，請稍後再試'
        })
      }
    },
    async addLike (restaurantId) {
      try {
        const { data } = await usersAPI.addLike({ restaurantId }) 

        if (data.status !== 'success') {
          throw new Error(data.message)
        }

        this.restaurant = {
          ...this.restaurant,
          isLiked: true
        }
      } catch (error) {
        console.log('error', error)
        Toast.fire({
          icon: 'error',
          title: '按讚失敗，請稍後再試'
        })
      }
    },
    async removeLike (restaurantId) {
      try {
        const { data } = await usersAPI.deleteLike({ restaurantId }) 

        if (data.status !== 'success') {
          throw new Error(data.message)
        }

         this.restaurant = {
          ...this.restaurant,
          isLiked: false
        }
      } catch (error) {
        console.log('error', error)
        Toast.fire({
          icon: 'error',
          title: '取消按讚失敗，請稍後再試'
        })
      }
    }
  }
}
</script>