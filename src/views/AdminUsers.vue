<template>
  <div class="container py-5">
    <AdminNav />

    <table class="table">
      <thead class="thead-dark">
        <tr>
          <th scope="col">
            #
          </th>
          <th scope="col">
            Email
          </th>
          <th scope="col">
            Role
          </th>
          <th
            scope="col"
            width="140"
          >
            Action
          </th>
        </tr>
      </thead>
      <tbody>
        <tr 
          v-for="user in users"
          :key="user.id"
        >
          <th scope="row">
            {{user.id}}
          </th>
          <td>{{user.email}}</td>
          <template>
            <td 
              v-if="user.isAdmin"
            >
              admin
            </td>
            <td 
              v-else
            >
              user
            </td>
          </template>
          <template
           v-if="user.id === currentUser.id"
          >
          </template>
          <template
           v-else 
          >
            <td
              v-if="user.isAdmin"
              @click.prevent.stop="toggleUserRole(user.id)"
            >
              <button
                type="button"
                class="btn btn-link"
              >
                set as user
              </button> 
            </td>
            <td
              v-else
            >
              <button
                type="button"
                class="btn btn-link"
                @click.prevent.stop="toggleUserRole(user.id)"
              >
                set as admin
              </button>
            </td>
          </template>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import AdminNav from '../components/AdminNav'
import adminAPI from '../apis/admin'
import { Toast } from '../utils/helpers'
import { mapState } from 'vuex'



export default {
  name: 'AdminUsers',
  components: {
    AdminNav
  },
  data () {
    return {
      users: []
    }
  },
  computed: {
    ...mapState(['currentUser'])
  },
  created () {
    this.fetchUsers()
    this.fetchCurrentUser()
  },
  methods: {
    async fetchUsers () {
      try {
        const { data } = await adminAPI.users.get()

        if (data.status === 'error') {
          throw new Error(data.message)
        }

        this.users = data.users
      } catch (error) {
        console.error(error.message)
        Toast.fire({
          icon: 'error',
          title: '資料載入失敗，請稍後再試'
        })
      }
    },
    toggleUserRole (userId) {
      this.users = this.users.map(user => {
        if (user.id === userId) {
          return{
            ...user,
            isAdmin: !user.isAdmin
          }
        }
        return user
      })
    }
  }
}
</script>