<template>
  <div class="container py-5">
    <UserProfileCard 
    :user="user"
    :initialisFollowed="isFollowed"
    :is-current-user="currentUser.id === user.id"
    />
    <div class="row">
      <div class="col-md-4">
        <UserFollowingsCard
        :followings="followings"
        />
        <br>
        <UserFollowersCard 
        :followers="followers"
        />
      </div>
      <div class="col-md-8">
        <UserCommentsCard 
        :comments="comments"
        />
        <br>
        <UserFavoritedRestaurantsCard 
        :favoritedRestaurants="favoritedRestaurants"
        />
      </div>
    </div>
  </div>
</template>

<script>
import UserProfileCard from '../components/UserProfileCard'
import UserFollowingsCard from '../components/UserFollowingsCard'
import UserFollowersCard from '../components/UserFollowersCard'
import UserCommentsCard from '../components/UserCommentsCard'
import UserFavoritedRestaurantsCard from '../components/UserFavoritedRestaurantsCard'
import usersAPI from '../apis/users'
import { Toast } from '../utils/helpers'
import { mapState } from 'vuex'



export default {
  name: 'User',
  components: {
    UserProfileCard,
    UserFollowingsCard,
    UserFollowersCard,
    UserCommentsCard,
    UserFavoritedRestaurantsCard
  },
  data () {
    return {
      user: {
        id: 0,
        image: '',
        name: '',
        email: '',
        followingsLength: 0,
        followersLength: 0,
        commentsLength: 0,
        favoritedRestaurantsLength: 0
      },
      followings: [],
      followers: [],
      comments: [],
      favoritedRestaurants: [],
      isFollowed: false
    }
  },
  computed: {
    ...mapState(['currentUser'])
  },
  beforeRouteUpdate (to, from, next) {
    const { id } = to.params
    this.fetchUser(id)
    next()
  },
  methods: {
    async fetchUser (userId) {
      try {
        const { data } = await usersAPI.get({ userId })

        if (data.status === 'error') {
          throw new Error(data.message)
        }

        console.log({data})
        console.log(this.currentUser)

        const {profile, isFollowed} = data
        const {id, image, name, email, Comments, Followers, Followings, FavoritedRestaurants} = profile

        const commentSet = new Set()
        this.comments = Comments.filter(
          comment =>
            comment.Restaurant &&
            !commentSet.has(comment.Restaurant.id) &&
            commentSet.add(comment.Restaurant.id)
        )


        this.user = {
          ...this.user,
          id,
          image,
          name,
          email,
          followingsLength: Followings.length,
          followersLength: Followers.length,
          commentsLength: Comments.length,
          favoritedRestaurantsLength: FavoritedRestaurants.length
        }
        this.followings = Followings
        this.followers = Followers
        this.comments = Comments
        this.favoritedRestaurants = FavoritedRestaurants
        this.isFollowed = isFollowed     
        console.log(this.isFollowed)   
      } catch (error) {
        console.error(error.message)
        Toast.fire({
          icon: 'error',
          title: '資料載入失敗，請稍後再試'
        })
      }
    }
  },
  created() {
    const { id: userId } = this.$route.params
    this.fetchUser(userId)
  }
}
</script>