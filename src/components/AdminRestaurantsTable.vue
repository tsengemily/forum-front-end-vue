<template>
  <table class="table">
    <thead class="thead-dark">
      <tr>
        <th scope="col">#</th>
        <th scope="col">Category</th>
        <th scope="col">Name</th>
        <th scope="col" width="300">操作</th>
      </tr>
    </thead>
    <tbody>
      <tr 
      v-for="restaurant in restaurants"
      :key="restaurant.id"
      >
        <th scope="row">{{restaurant.id}}</th>
        <td>{{restaurant.Category ? restaurant.Category.name : '未分類'}}</td>
        <td>{{restaurant.name}}</td>
        <td class="d-flex justify-content-between">
          <router-link 
            class="btn btn-link"
            :to="{name: 'admin-restaurant', params: {id: restaurant.id}}"
          >
            Show
          </router-link>
          <router-link 
          class="btn btn-link"
          :to="{name: 'admin-restaurant-edit', params: {id: restaurant.id}}"
          >
            Edit
          </router-link>  
          <button type="button" class="btn btn-link" @click.stop.prevent="deleteRestaurant(restaurant.id, restaurant.name)">Delete</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import adminAPI from '../apis/admin'
import { Toast } from '../utils/helpers'


export default {
  data () {
    return {
      restaurants: []
    }
  },
  methods: {
    async fetchRestaurants () {
        try {
            const { data } = await adminAPI.restaurants.get()
            this.restaurants = data.restaurants
        } catch (error) {
            console.log(error)
            Toast.fire({
                icon: 'error',
                title: '抓取資料失敗，請稍後再試'
            })
        }
    },
    async deleteRestaurant (restaurantId, restaurantName) {
        try {
            const { data } = await adminAPI.restaurants.delete({ restaurantId })
            if (data.status !== 'success') {
                throw new Error(data.message)
            }
            Toast.fire({
                icon: 'success',
                title: `移除${restaurantName}`
            })
            this.restaurants = this.restaurants.filter(restaurant => restaurant.id !== restaurantId)  
        } catch (error) {
            console.log(error)
            Toast.fire({
                icon: 'error',
                title: '抓取資料失敗，請稍後再試'
            })
        }
    }
  },
  created () {
    this.fetchRestaurants()
  }
}
</script>