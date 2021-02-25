<template>
  <div class="container py-5">
    <NavTabs />

    <h1 class="mt-5">
      最新動態
    </h1>
    <hr>
    <div class="row">
      <div class="col-md-6">
        <h3>最新餐廳</h3>
        <!-- 最新餐廳 NewestRestaurants -->
        <NewestRestaurants :restaurants="restaurants"/>
      </div>
      <div class="col-md-6">
        <!-- 最新評論 NewestComments-->
        <h3>最新評論</h3>
        <NewestComment :comments="comments"/>
      </div>
    </div>
  </div>
</template>

<script>
import NavTabs from '../components/NavTabs'
import NewestRestaurants from '../components/NewestRestaurants'
import NewestComment from '../components/NewestComment'
import restaurantsAPI from '../apis/restaurants'
import {Toast} from '../utils/helpers'


export default {
  components: {
    NavTabs,
    NewestRestaurants,
    NewestComment
  },
  data () {
    return {
      restaurants : [],
      comments : []
    }
  },
  methods: {
      async fetchFeeds () {
        try {
          const response = await restaurantsAPI.getFeeds()
          const { restaurants, comments } = response.data
          this.restaurants = restaurants
          this.comments = comments
        } catch (error) {
              Toast.fire({
                  icon: 'error',
                  title: '無法取得資料，請確認連線'
              })
              console.log (error)
          } 
      } 
  },
  created () {
    this.fetchFeeds()
  }
}
</script>