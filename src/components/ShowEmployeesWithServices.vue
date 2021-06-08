<template>
  <v-container>
    <v-list v-for="(employee, index) in employees" v-bind:key="employee.id">
        <Employee :password="employee.password" :reloadMethod="reloadEmployees" :name="employee.name" :emp-id="employee.id" :latitude="employee.latitude" :longitude="employee.longitude" :employees="employees"/>
        <v-divider
          v-if="index < employees.length - 1"
          :key="index"
        ></v-divider>
    </v-list>

    <v-dialog
      v-model="showEmployeeAddDialog"
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
            @click="showEmployeeAddDialog = false"
          >
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>Neuen Mitarbeiter hinzufügen</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn
              dark
              text
              @click="newEmployeeSaveButtonClicked"
            >
              Speichern
            </v-btn>
          </v-toolbar-items>
        </v-toolbar>
        <v-list
          three-line
          subheader
        >
          <v-subheader>Neuer Mitarbeiter</v-subheader>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-subtitle><v-text-field v-model="newEmployeeName" label="Name"></v-text-field></v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-subtitle><v-text-field v-model="newEmployeePassword"
                                                  label="Passwort"
                                                  :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
                                                  :rules="[rules.required, rules.min]"
                                                  :type="showPassword ? 'text' : 'password'"
                                                  hint="At least 8 characters"
                                                  counter
                                                  @click:append="showPassword = !showPassword"
                                                  ></v-text-field></v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-subtitle><v-text-field v-model="newEmployeeAddress" label="Adresse"></v-text-field></v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-card>
    </v-dialog>

    <v-btn
      class="mx-2"
      fab
      dark
      color="primary"
      @click="showEmployeeAddDialog = true"
      style="position: fixed;
             right: 25px;
             bottom: 25px;"
    >
      <v-icon dark>
        mdi-plus
      </v-icon>
    </v-btn>

  </v-container>
</template>

<script>
import Employee from '@/components/Employee'
import axios from 'axios'
export default {
  name: 'ShowEmployeesWithServices',
  components: {
    Employee
  },
  data () {
    return {
      employees: [],
      showEmployeeAddDialog: false,
      showPassword: false,
      newEmployeeName: '',
      newEmployeePassword: '',
      newEmployeeAddress: '',
      rules: {
        required: value => !!value || 'Required.',
        min: v => v.length >= 8 || 'Min 8 characters'
      }
    }
  },
  mounted () {
    this.reloadEmployees()
  },
  methods: {
    reloadEmployees: function () {
      axios
        .get('http://localhost:9000/employees')
        .then(response => (this.employees = response.data))
        .then(_ => console.log(this.employees))
    },
    newEmployeeSaveButtonClicked: function () {
      console.log('New Employee! -> Name: ' + this.newEmployeeName + ' Address:' + this.newEmployeeAddress + ' Password: ' + this.newEmployeePassword)

      const jsonStr = '' +
        '{' +
        '"name": "' + this.newEmployeeName + '",' +
        '"address": "' + this.newEmployeeAddress + '",' +
        '"password": "' + this.newEmployeePassword + '"' +
        '}'

      console.log(jsonStr)
      const url = 'http://localhost:9000/employees'
      console.log(url)
      axios.post(url, jsonStr, { headers: { 'Content-Type': 'application/json' } })
        .catch(function (error) {
          alert(error.response.data.message)
        })
        .then(response => {
          if (response.status === 200) {
            this.showEmployeeAddDialog = false
            this.newEmployeeName = ''
            this.newEmployeePassword = ''
            this.newEmployeeAddress = ''
            this.reloadEmployees()
          }
        })
    }
  }
}
</script>

<style scoped>

</style>
