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
              @click="show = !show; showEdit = false"
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
                <Service :employee-id="service.employeeId" :employees="employees" :name="service.name" :date="service.date" :latitude="service.latitude" :longitude="service.longitude" :service-id="service.id" :address="service.address" :reload-method="reload"/>
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
            <div style="width: 100%" v-if="linkToGoogleMaps !== ''">
              <iframe width="100%" height="300" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" :src="linkToGoogleMaps"></iframe>
            </div>
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
                  <td>Adresse</td>
                  <td>{{ address }}</td>
                </tr>
                <tr>
                  <td>Mitarbeiter - ID</td>
                  <td>{{ empId }}</td>
                </tr>
                </tbody>
              </template>
            </v-simple-table>
            <v-btn
              class="ma-2"
              outlined
              color="primary"
              @click="deleteEmployee"
            >
              Mitarbeiter löschen
            </v-btn>
            <v-btn
              class="ma-2"
              outlined
              color="primary"
              @click="showEdit = true; show = false"
            >
              Mitarbeiter bearbeiten
            </v-btn>
          </v-card-text>
        </div>
      </v-expand-transition>
      <v-expand-transition>
        <div v-show="showEdit">
          <v-divider></v-divider>

          <v-card-text>
            <v-simple-table>
              <template v-slot:default>
                <tbody>
                <tr>
                  <td>Name</td>
                  <td>
                    <v-text-field
                      label="Name"
                      :rules="rules"
                      hide-details="auto"
                      v-model="name"
                    ></v-text-field>
                  </td>
                </tr>
                <tr>
                  <td>Adresse</td>
                  <td>
                    <v-text-field
                      label="Neue Adresse"
                      :rules="rules"
                      hide-details="auto"
                      v-model="address"
                    ></v-text-field>
                  </td>
                </tr>
                <tr>
                  <td>Passwort</td>
                  <td>
                    <v-text-field
                      hide-details="auto"
                      label="Passwort"
                      :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
                      :rules="[rules.required, rules.min]"
                      :type="showPassword ? 'text' : 'password'"
                      hint="At least 8 characters"
                      counter
                      @click:append="showPassword = !showPassword"
                      v-model="password"
                    ></v-text-field>
                  </td>
                </tr>
                </tbody>
              </template>
            </v-simple-table>
            <v-btn
              class="ma-2"
              outlined
              color="primary"
              @click="editEmployee"
            >
              Speichern
            </v-btn>
            <v-btn
              class="ma-2"
              outlined
              color="primary"
              @click="showEdit = false; show = true; reloadMethod()"
            >
              Abbrechen
            </v-btn>
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
import Vue from 'vue'
import '../plugins/googleMap'
// import google from 'https://maps.googleapis.com/maps/api/js?key=AIzaSyCZVu8d3a4OUJMdGUcfOg5VH7fKoDMgG6w&callback=initMap&libraries=&v=weekly'
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
      address: String,
      newServiceAddress: '',
      showEdit: false,
      rules: {
        required: value => !!value || 'Required.',
        min: v => v.length >= 8 || 'Min 8 characters'
      },
      showPassword: false,
      linkToGoogleMaps: 'https://www.google.com/maps/embed/v1/place?key=AIzaSyARLS2AunIVNbrHvZwSNnvGRJMiLGN-Ugo&maptype=satellite&center=' + this.latitude + ',' + this.longitude + '&q=' + this.latitude + ',' + this.longitude + '&zoom=18'
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
    password: String,
    employees: [],
    reloadMethod: Function
  },
  methods: {
    reload: function () {
      axios
        .get('http://localhost:9001/services/emp/' + this.empId)
        .then(response => (this.services = response.data))

      axios
        .get('http://localhost:9002/locationiq/addresses?longitude=' + this.longitude + '&latitude=' + this.latitude)
        .then(response => (this.address = response.data.toString()))

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
      let day = dateObj.getDate()
      let month = dateObj.getMonth()
      month = month + 1
      if ((String(day)).length === 1) {
        day = '0' + day
      }
      if ((String(month)).length === 1) {
        month = '0' + month
      }

      return day + '.' + month + '.' + dateObj.getFullYear()
    },
    deleteEmployee: function () {
      console.log('DELETE!')
      axios.delete('http://localhost:9000/employees/' + this.empId)
        .then(response => this.handleDeleteResponse(response))
    },
    handleDeleteResponse: function (response) {
      if (response.status !== 200) {
        alert('Konnte den Mitarbeiter nicht löschen!')
      } else {
        this.reloadMethod()
      }
    },
    handleEditResponse: function (response) {
      if (response.status !== 200) {
        alert('Konnte den Mitarbeiter nicht bearbeiten!')
      } else {
        this.reloadMethod()
        this.show = false
        this.showEdit = false
      }
    },
    editEmployee: function () {
      console.log('Editing!')
      const jsonStr = '' +
        '{' +
        '"name": "' + this.name + '",' +
        '"employeeId": ' + this.empId + ',' +
        '"address": "' + this.address + '",' +
        '"password": "' + this.password + '"' +
        '}'
      axios.put('http://localhost:9000/employees/' + this.empId, jsonStr, {
        headers: {
          'Content-Type': 'application/json'
        }
      })
        .then(response => this.handleEditResponse(response))
    }
  }
}
</script>

<style scoped>

</style>
