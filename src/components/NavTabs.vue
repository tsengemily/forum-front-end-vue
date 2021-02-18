<template>
  <ul class="nav nav-tabs mb-4">
    <li class="nav-item" v-for="tab in tabs" :key="tab.id">
      <router-link
        :to="tab.path"
        class="nav-link"
      >
        {{tab.name}}
      </router-link>
    </li>
  </ul>
</template>

<script>
import {v4 as uuidv4} from 'uuid'

export default {
  data () {
    return {
      tabs: [
        {
          id: uuidv4(),
          path: '/restaurants',
          name: '首頁'
        },
        {
          id: uuidv4(),
          path: '/restaurants/feeds',
          name: '最新動態'
        },
        {
          id: uuidv4(),
          path: '/restaurants/top',
          name: 'TOP 10 人氣餐廳'
        },
        {
          id: uuidv4(),
          path: '/users/top',
          name: '美食達人'
        }
      ]
    }
  }
}
</script>