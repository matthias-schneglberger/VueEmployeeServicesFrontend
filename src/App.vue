<template>
  <v-app>
    <v-main>
<!--      <router-view/>-->
      <ShowEmployeesWithServices/>
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue'
import ShowEmployeesWithServices from '@/components/ShowEmployeesWithServices.vue'

export default Vue.extend({
  name: 'App',
  components: {
    ShowEmployeesWithServices
  },
  data: () => ({
    //
  })
})
</script>
