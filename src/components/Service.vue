<template>
    <v-list-item-content>
      <v-container style="padding: 0">
        <v-row>
          <v-col cols="8" align-self="center">
            {{name}}
          </v-col>
          <v-col cols="1">
            <v-btn icon @click="show = !show" >
              <v-icon>{{ show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="1">
            <v-btn icon @click="deleteService" >
              <v-icon>{{ show ? 'mdi-cancel' : 'mdi-cancel' }}</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="1">
            <v-btn icon >
              <v-icon>{{ icons.mdiPencil }}</v-icon>
            </v-btn>
          </v-col>
        </v-row>
        <v-expand-transition>
          <div v-show="show">
            <v-divider></v-divider>

            <v-card-text>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                  <tr>
                    <th class="text-left">
                      Property
                    </th>
                    <th class="text-left">
                      Value
                    </th>
                  </tr>
                  </thead>
                  <tbody>
                  <tr>
                    <td>Latitude</td>
                    <td>{{ latitude }}</td>
                  </tr>
                  <tr>
                    <td>Longitude</td>
                    <td>{{ longitude }}</td>
                  </tr>
                  <tr>
                    <td>Datum</td>
                    <td>{{ date }}</td>
                  </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-card-text>
          </div>
        </v-expand-transition>
      </v-container>
    </v-list-item-content>
</template>

<script>
import axios from 'axios'
import {
  mdiPencil,
  mdiDelete
} from '@mdi/js'
export default {
  name: 'Service',
  data () {
    return {
      show: false,
      icons: {
        mdiPencil,
        mdiDelete
      }
    }
  },
  props: {
    name: String,
    longitude: String,
    latitude: String,
    date: String,
    serviceId: String,
    employeeId: String,
    reloadMethod: Function
  },
  methods: {
    deleteService: function () {
      console.log('DELETE!')
      axios.delete('http://localhost:9001/services/' + this.serviceId)
        .then(response => this.handleDeleteResponse(response))
    },
    handleDeleteResponse: function (response) {
      if (response.status !== 200) {
        alert('Konnte den Service nicht löschen!')
      } else {
        this.reloadMethod()
      }
    }
  }
}
</script>

<style scoped>

</style>
