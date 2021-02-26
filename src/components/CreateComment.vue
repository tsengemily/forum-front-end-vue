<template>
  <form
  @submit.stop.prevent="handleSubmit"
  >
    <div class="form-group mb-4">
      <label for="text">留下評論：</label>
      <textarea
        class="form-control"
        rows="3"
        name="text"
        v-model="text"
      />
    </div>
    <div class="d-flex align-items-center justify-content-between">
      <button
        type="button"
        class="btn btn-link"
        @click="$router.back()"
      >回上一頁</button>
      <button
        type="submit"
        class="btn btn-primary mr-0"
        :disabled="isProcessing"
      >
        {{isProcessing ? '處理中...' : 'Submit'}}
      </button>
    </div>
  </form>
</template>

<script>
import commentsAPI from '../apis/comments'
import { Toast } from '../utils/helpers'

export default {
  name: 'CreateComment',
  props: {
    restaurantId: {
      type: Number,
      required: true
    } 
  },
  data () {
    return {
      text: '',
      isProcessing: false
    }
  },
  methods: {
    async handleSubmit () {
      try {
        this.isProcessing = true

        if (!this.text) {
          Toast.fire({
            icon: 'warning',
            title: '請輸入評論'
          })
        return
        }

        const { data } = await commentsAPI.create({
          restaurantId: this.restaurantId,
          text: this.text
        })

        if (data.status !== 'success') {
          throw new Error(data.message)
        } 

        this.$emit('after-create-comment', {
          commentId: data.commentId,
          restaurantId: this.restaurantId,
          text: this.text
        })

        this.isProcessing = false
        this.text = ''
      } catch (error) {
        console.log('error', error)
        this.isProcessing = false
        Toast.fire({
          icon: 'error',
          title: '新增失敗，請稍後再試'
        })
      }
    }
  }
}
</script>