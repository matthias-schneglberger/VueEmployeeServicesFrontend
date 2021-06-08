<template>
    <v-list-item-content>
      <v-container style="padding: 0">
        <v-row>
          <v-col cols="8" align-self="center">
            {{name}}
          </v-col>
          <v-col cols="1">
            <v-btn icon @click="show = !show; show ? showEdit = false : showEdit" >
              <v-icon>{{ show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="1">
            <v-btn icon @click="deleteService" >
              <v-icon>{{ show ? 'mdi-cancel' : 'mdi-cancel' }}</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="1">
            <v-btn icon @click="showEdit = !showEdit; showEdit ? show = false : show">
              <v-icon>{{ icons.mdiPencil }}</v-icon>
            </v-btn>
          </v-col>
        </v-row>
        <v-expand-transition>
          <div v-show="show">
            <v-divider></v-divider>
            <div style="width: 100%" v-if="linkToGoogleMaps !== ''">
              <iframe width="100%" height="300" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" :src="linkToGoogleMaps"></iframe>
            </div>
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
                    <td>Adresse</td>
                    <td>{{ address }}</td>
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
                    <td>Mitarbeiter</td>
                    <td>
                      <v-select
                        :items="employees"
                        item-text="name"
                        label="Mitarbeiter:"
                        v-model="employeeId"
                        item-value="id"
                      ></v-select>
                    </td>
                  </tr>
                  <tr>
                    <td>Datum</td>
                    <td>
                      <v-text-field
                      label="Datum bearbeiten"
                      :rules="rules"
                      hide-details="auto"
                      v-model="date"
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
                @click="editService"
              >
                Speichern
              </v-btn>
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
      showEdit: false,
      icons: {
        mdiPencil,
        mdiDelete
      },
      address: '',
      linkToGoogleMaps: 'https://www.google.com/maps/embed/v1/place?key=AIzaSyARLS2AunIVNbrHvZwSNnvGRJMiLGN-Ugo&maptype=satellite&center=' + this.latitude + ',' + this.longitude + '&q=' + this.latitude + ',' + this.longitude + '&zoom=18'
    }
  },

  mounted () {
    axios
      .get('http://localhost:9002/locationiq/addresses?longitude=' + this.longitude + '&latitude=' + this.latitude)
      .then(response => (this.address = response.data.toString()))
  },
  props: {
    name: String,
    longitude: String,
    latitude: String,
    date: String,
    serviceId: String,
    employeeId: String,
    reloadMethod: Function,
    employees: []
  },
  methods: {
    deleteService: function () {
      console.log('DELETE!')
      axios.delete('http://localhost:9001/services/' + this.serviceId)
        .then(response => this.handleDeleteResponse(response))
    },
    editService: function () {
      console.log('Editing!')
      const jsonStr = '' +
        '{' +
        '"name": "' + this.name + '",' +
        '"employeeId": ' + this.employeeId + ',' +
        '"address": "' + this.address + '",' +
        '"date": "' + this.date + '"' +
        '}'
      axios.put('http://localhost:9001/services/' + this.serviceId, jsonStr, {
        headers: {
          'Content-Type': 'application/json'
        }
      }).then(response => this.handleEditResponse(response))
    },
    handleEditResponse: function (response) {
      if (response.status !== 200) {
        alert('Konnte den Service nicht bearbeiten!')
      } else {
        this.reloadMethod()
        this.showEdit = false
      }
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
