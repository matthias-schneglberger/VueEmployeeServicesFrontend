<template>
  <v-list-item-content>
<!--    <v-list-item-title v-text="sampleText"></v-list-item-title>-->
    <v-list-item-content style="padding: 0">
      <v-container style="padding: 0">
        <v-row>
          <v-col cols="8" align-self="center">
            {{name}}
          </v-col>
          <v-col cols="2">
            <v-btn
              icon
              @click="show = !show"
              width="50%"
            >
              Details anzeigen
            </v-btn>

          </v-col>
        </v-row>
      </v-container>
      <v-expand-transition>
        <div v-show="show">
          <v-divider></v-divider>

          <v-card-text>
            asdfasdfasdf
          </v-card-text>
        </div>
      </v-expand-transition>
    </v-list-item-content>

  </v-list-item-content>
</template>

<script>
export default {
  name: 'Employee',
  data () {
    return {
      sampleText: 'test',
      show: false,
      center: { lat: 40.689247, lng: -74.044502 }
    }
  },
  mounted () {
    console.log('Employee')
  },
  props: {
    name: String,
    empId: String,
    longitude: String,
    latitude: String,
    address: String
  },
  methods: {
    showDetails: function () {
      this.show = !this.show
    }
  }
}
</script>

<style scoped>

</style>
