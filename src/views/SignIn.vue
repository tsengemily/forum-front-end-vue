<template>
  <div class="container py-5">
    <form class="w-100" @submit.prevent.stop = "handlerSubmit">
      <div class="text-center mb-4">
        <h1 class="h3 mb-3 font-weight-normal">
          Sign In
        </h1>
      </div>

      <div class="form-label-group mb-2">
        <label for="email">email</label>
        <input
          id="email"
          v-model="email"
          name="email"
          type="email"
          class="form-control"
          placeholder="email"
          autocomplete="username"
          required
          autofocus
        >
      </div>

      <div class="form-label-group mb-3">
        <label for="password">Password</label>
        <input
          id="password"
          v-model="password"
          name="password"
          type="password"
          class="form-control"
          placeholder="Password"
          autocomplete="current-password"
          required
        >
      </div>

      <button
        class="btn btn-lg btn-primary btn-block mb-3"
        type="submit"
        :disabled="isProcessing"
      >
        Submit
      </button>

      <div class="text-center mb-3">
        <p>
          <router-link to="/signup" >Sign Up</router-link>
        </p>
      </div>

      <p class="mt-5 mb-3 text-muted text-center">
        &copy; 2017-2018
      </p>
    </form>
  </div>
</template>

<script>
import authorizationAPI from '../apis/authorization'
import { Toast } from '../utils/helpers'

export default {
  data() {
    return {
      email: '',
      password: '',
      isProcessing: false
    }
  },
  methods: {
    async handlerSubmit () {
      try {
        if (!this.email || !this.password) {
          Toast.fire({
            icon: 'warning',
            title: '請輸入帳號密碼'
          })
          return
        }
        this.isProcessing = true
        const response = await authorizationAPI.signIn({
          email: this.email,
          password: this.password
        })
        const {data} = response
        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        localStorage.setItem('token', data.token)
        this.$router.push('/restaurants')
      } catch (error) {
        this.password = ""
          Toast.fire({
            icon: 'warning',
            title: '帳號密碼有誤，請再次確認'
          })
          this.isProcessing = false
          console.log(error)
      }
    }
    // handlerSubmit () {
    //   if (!this.email || !this.password) {
    //     Toast.fire({
    //       icon: 'warning',
    //       title: '請輸入帳號密碼'
    //     })
    //     return
    //   }
    //   this.isProcessing = true
    //   authorizationAPI.signIn({
    //     email: this.email,
    //     password: this.password
    //   }).then(response => {
    //     console.log(response)
    //     const {data} = response
    //     if (data.status !== "success") {
    //       throw new Error(data.message)
    //     }
    //     localStorage.setItem('token', data.token)
    //     this.$router.push('/restaurants')
    //   }).catch(error => {
    //       this.password = ""
    //       Toast.fire({
    //         icon: 'warning',
    //         title: '帳號密碼有誤，請再次確認'
    //       })
    //       this.isProcessing = false
    //       console.log(error)
    //   })
    // }
  }
}
</script>