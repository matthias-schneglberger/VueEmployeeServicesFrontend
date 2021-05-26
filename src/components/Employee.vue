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
                <Service :name="service.name" :date="service.date" :latitude="service.latitude" :longitude="service.longitude" :service-id="service.id" :reload-method="reload"/>
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
              <v-btn
                color="blue darken-1"
                text
                @click="showServiceAddDialog = true"
              >
                Neuer Service
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
                  <th class="text-left" width="50%">
                    Feld
                  </th>
                  <th class="text-left" colspan="1">
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
      <v-dialog
        v-model="showServiceAddDialog"
        fullscreen
        hide-overlay
        transition="dialog-bottom-transition"
      >
        <v-card>
          <v-toolbar
            dark
            color="primary"
          >
            <v-btn
              icon
              dark
              @click="showServiceAddDialog = false"
            >
              <v-icon>mdi-close</v-icon>
            </v-btn>
            <v-toolbar-title>Neuen Service hinzufügen</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
              <v-btn
                dark
                text
                @click="newServiceSaveButtonClicked"
              >
                Speichern
              </v-btn>
            </v-toolbar-items>
          </v-toolbar>
          <v-list
            three-line
            subheader
          >
            <v-subheader>Neuer Service</v-subheader>
            <v-list-item>
              <v-list-item-content>
                <v-list-item-subtitle>
                  <v-select
                    :items="employees"
                    item-text="name"
                    label="Mitarbeiter:"
                    v-model="newServiceSelectedEmployee"
                    item-value="id"
                  ></v-select>
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <v-list-item>
              <v-list-item-content>
                <v-list-item-subtitle><v-text-field v-model="newServiceName" label="Service Name"></v-text-field></v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <v-list-item>
              <v-list-item-content>
                <v-list-item-subtitle><v-text-field v-model="newServiceAddress" label="Adresse eingeben"></v-text-field></v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <v-list-item>
              <v-list-item-content>
                <v-list-item-subtitle>
                  <v-menu
                    v-model="newServiceShowMenuDate"
                    :close-on-content-click="false"
                    :nudge-right="40"
                    transition="scale-transition"
                    offset-y
                    min-width="auto"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                        v-model="newServiceDate"
                        label="Datum eingeben:"
                        prepend-icon="mdi-calendar"
                        readonly
                        v-bind="attrs"
                        v-on="on"
                      ></v-text-field>
                    </template>
                    <v-date-picker
                      v-model="newServiceDate"
                      @input="newServiceShowMenuDate = false"
                    ></v-date-picker>
                  </v-menu>
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <v-list-item>
              <v-list-item-content>
                <v-list-item-subtitle>
                  <v-menu
                    ref="menu"
                    v-model="newServiceShowMenuTime"
                    :close-on-content-click="false"
                    :nudge-right="40"
                    :return-value.sync="newServiceTime"
                    transition="scale-transition"
                    offset-y
                    max-width="290px"
                    min-width="290px"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                        v-model="newServiceTime"
                        label="Zeit eingeben"
                        prepend-icon="mdi-clock-time-four-outline"
                        readonly
                        v-bind="attrs"
                        v-on="on"
                      ></v-text-field>
                    </template>
                    <v-time-picker
                      v-if="newServiceShowMenuTime"
                      v-model="newServiceTime"
                      full-width
                      format="24hr"
                      @click:minute="$refs.menu.save(newServiceTime)"
                    ></v-time-picker>
                  </v-menu>
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-list>
        </v-card>
      </v-dialog>
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
      showServiceAddDialog: false,
      show: false,
      dialog: false,
      services: [],
      newServiceDate: new Date().toISOString().substr(0, 10),
      newServiceShowMenuDate: false,
      newServiceSelectedEmployee: String,
      newServiceTime: null,
      newServiceShowMenuTime: false,
      newServiceName: '',
      newServiceAddress: ''
    }
  },

  mounted () {
    this.reload()
  },
  props: {
    name: String,
    empId: String,
    longitude: String,
    latitude: String,
    address: String,
    employees: []
  },
  methods: {
    reload: function () {
      axios
        .get('http://localhost:9001/services/emp/' + this.empId)
        .then(response => (this.services = response.data))

      this.newServiceSelectedEmployee = this.empId
    },
    newServiceSaveButtonClicked: function () {
      console.log('New Service! -> EmpID: ' + this.newServiceSelectedEmployee + ' ServiceName:' + this.newServiceName + ' ServiceAddresse: ' + this.newServiceAddress + ' Date: ' + this.newServiceDate + ' Time: ' + this.newServiceTime)

      const jsonStr = '' +
        '{' +
        '"name": "' + this.newServiceName + '",' +
        '"employeeId": ' + this.newServiceSelectedEmployee + ',' +
        '"address": "' + this.newServiceAddress + '",' +
        '"date": "' + this.parseDateInString(new Date(this.newServiceDate)) + ' ' + this.newServiceTime + '"' +
        '}'

      const url = 'http://localhost:9001/services'
      console.log(url)
      axios.post(url, jsonStr, { headers: { 'Content-Type': 'application/json' } })
        .catch(function (error) {
          alert(error.response.data.message)
        })
        .then(response => {
          if (response.status === 200) {
            this.showServiceAddDialog = false
            this.reload()
          }
        })
    },
    parseDateInString: function (dateObj) {
      let day = dateObj.getDate();
      let month = dateObj.getMonth();
      month = month + 1
      if ((String(day)).length === 1) {
        day = '0' + day
      }
      if ((String(month)).length === 1) {
        month = '0' + month
      }

      return day + '.' + month + '.' + dateObj.getFullYear()
    }
  }
}
</script>

<style scoped>

</style>
