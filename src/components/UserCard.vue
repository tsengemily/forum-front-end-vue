 <template>
      <div class="col-3">
        <a href="#">
          <img
            :src="user.image"
            width="140px"
            height="140px"
          >
        </a>
        <h2>{{user.name}}</h2>
        <span class="badge badge-secondary">追蹤人數：{{user.FollowerCount}}</span>
        <p class="mt-3">
          <button
            type="button"
            class="btn btn-danger"
            v-if="user.isFollowed"
            @click="removeFollow"
          >
            取消追蹤
          </button>
          <button
            type="button"
            class="btn btn-primary"
            v-else
            @click="addFollow"
          >
            追蹤
          </button>
        </p>
      </div>
 </template>



<script>
export default {
  props: {
    initUser: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      user: this.initUser
    }
  },
  methods: {
    addFollow () {
      const newFollowerCount =  this.user.FollowerCount + 1
      this.user = {
        ...this.user,
        isFollowed: true,
        FollowerCount: newFollowerCount
      }
    },
    removeFollow () {
       const newFollowerCount =  this.user.FollowerCount - 1
      this.user = {
        ...this.user,
        isFollowed: false,
        FollowerCount: newFollowerCount
      }
    }
  } 
}
</script>