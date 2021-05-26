<template>
  <v-container>
    <v-list v-for="(employee, index) in employees" v-bind:key="employee.id">
        <Employee :name="employee.name" :emp-id="employee.id" :latitude="employee.latitude" :longitude="employee.longitude" :employees="employees"/>
        <v-divider
          v-if="index < employees.length - 1"
          :key="index"
        ></v-divider>
    </v-list>
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
      employees: []
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
    }
  }
}
</script>

<style scoped>

</style>
