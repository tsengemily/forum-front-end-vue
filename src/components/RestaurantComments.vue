<template>
  <div>
    <h2 class="my-4">
      所有評論：
    </h2>

    <div 
    v-for="comment in restaurantComments" :key="comment.id">
      <blockquote class="blockquote mb-0">
        <button
          type="button"
          class="btn btn-danger float-right"
          v-if="currentUser.isAdmin"
          @click.stop.prevent="handleDeleteButtonClick(comment.id)"
        >
          Delete
        </button>
        <h3>
          <router-link :to="{name: 'user', params: {id: comment.User.id}}">
            {{comment.User.name}}
          </router-link>
        </h3>
        <p>{{comment.text}}</p>
        <footer class="blockquote-footer">
          {{comment.createdAt | fromNow}}
        </footer>
      </blockquote>
      <hr>
    </div>
  </div>
</template>

<script>
import {fromNowFilter} from '../utils/mixins'
import { mapState } from 'vuex'
import commentsAPI from '../apis/comments'
import { Toast } from '../utils/helpers'



export default {
  name: 'Comments',
  props: {
    restaurantComments: {
      type: Array,
      required: true
    }
  },
  computed: {
    ...mapState(['currentUser'])
  },
  mixins: [fromNowFilter],
  methods: {
    async handleDeleteButtonClick(commentId) {
      try {
        const { data } = await commentsAPI.delete({commentId})
        
        if (data.status !== 'success') {
          throw new Error(data.message)
        }

        Toast.fire({
          icon: 'success',
          title: '刪除成功'
        })
      } catch (error) {
        console.log('error', error)
        Toast.fire({
          icon: 'error',
          title: '刪除失敗，請稍後再試'
        })
        
      }
      this.$emit('after-delete-comment', commentId)
    }
  }
}
</script>