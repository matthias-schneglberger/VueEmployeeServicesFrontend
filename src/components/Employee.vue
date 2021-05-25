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
          <v-col cols="2">
            <v-btn
              icon
              @click="dialog = !dialog"
              width="50%"
            >
              {{ services.length }} Services
            </v-btn>
          </v-col>
        </v-row>
        <v-dialog
          v-model="dialog"
          max-width="600px"
        >
          <v-card>
            <v-card-title>
              <span class="headline">Services von: {{ name }}</span>
            </v-card-title>
            <v-card-text>
              <v-list v-for="(service, index) in services" v-bind:key="service.id">
                <Service :name="service.name" :date="service.date" :latitude="service.latitude" :longitude="service.longitude" :service-id="service.id"/>
                <v-divider
                  v-if="index < services.length - 1"
                  :key="index"
                ></v-divider>
              </v-list>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="dialog = false"
              >
                Schließen
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-container>
      <v-expand-transition>
        <div v-show="show">
          <v-divider></v-divider>

          <v-card-text>
            <div id="map">Here should be the map</div>
            <v-simple-table>
              <template v-slot:default>
                <thead>
                <tr>
                  <th class="text-left">
                    Feld
                  </th>
                  <th class="text-left">
                    Wert
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
                  <td>Mitarbeiter - ID</td>
                  <td>{{ empId }}</td>
                </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-card-text>
        </div>
      </v-expand-transition>
    </v-list-item-content>

  </v-list-item-content>
</template>

<script>
import axios from 'axios'
import Service from '@/components/Service'
export default {
  name: 'Employee',
  components: {
    Service
  },
  data () {
    return {
      sampleText: 'test',
      show: false,
      dialog: false,
      services: []
    }
  },

  mounted () {
    console.log('Employee')
    axios
      .get('http://localhost:9002/services/emp/' + this.empId)
      .then(response => (this.services = response.data))
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
