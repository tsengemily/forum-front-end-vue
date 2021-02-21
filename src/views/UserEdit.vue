<template>
  <div class="container py-5">
    <form
      @submit.stop.prevent="handleSubmit"
    >
      <div class="form-group">
        <label for="name">Name</label>
        <input
          id="name"
          type="text"
          name="name"
          class="form-control"
          placeholder="Enter Name"
          required
          v-model="user.name"
        >
      </div>

      <div class="form-group">
        <label for="image">Image</label>
        <img 
          :src="user.image" 
          v-if="user.image"
          class="d-block img-thumbnail mb-3"
          width="200"
          height="200"
        >
        <input
          id="image"
          type="file"
          name="image"
          accept="image/*"
          class="form-control-file"
          @change="handleFileChange"
        >
      </div>

      <button
        type="submit"
        class="btn btn-primary"
      >
        Submit
      </button>
    </form>
  </div>
</template>

<script>
const dummyUser = {
  currentUser: {
    id: 1,
    name: '管理者',
    email: 'root@example.com',
    image: 'https://i.pravatar.cc/300',
    isAdmin: true
  }
}


export default {
  data () {
    return {
      user: {
        id: -1,
        name: '',
        image: ''
      }
    }
  },
  methods: {
    fetchUser (userId) {
      const {currentUser} = dummyUser
      const {name, image} = currentUser
      this.user = {
        ...this.user,
        name,
        image
      }
      console.log(userId)
    },
    handleFileChange (e) {
      const files = e.target.files
      if (files.length === 0) {
        return
      } else {
        const imageURL = window.URL.createObjectURL(files[0])
        this.user.image = imageURL
      }
    },
    handleSubmit (e) {
      const form = e.target
      const formData = new FormData(form)
      for (let [name, value] of formData.entries()) {
        console.log(name + ': ' + value)
      }
    }
  },
  created () {
    const {id} = this.$route.params
    this.fetchUser(id)
  }
}
</script>