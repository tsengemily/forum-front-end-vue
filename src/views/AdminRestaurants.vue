<template>
  <div class="container py-5">
    <AdminNav />
    <router-link 
      class="btn btn-primary mb-4"
      to= "/admin/restaurants/new"
    >
      New Restaurant
    </router-link>
    <AdminRestaurantsTable />
  </div>
</template>

<script>
import AdminNav from '../components/AdminNav'
import AdminRestaurantsTable from '../components/AdminRestaurantsTable'

export default {
  components: {
    AdminNav,
    AdminRestaurantsTable
  }
  
}
</script>