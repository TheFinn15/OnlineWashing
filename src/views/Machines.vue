<template>
  <v-app>
    <v-card style="margin: 5% 15% 0 15%">
      <v-text-field
          @input="searchMachine"
          v-model="searchVal"
          label="Введите название стиральной машини"
          rounded
      >
        <v-icon slot="append">search</v-icon>
      </v-text-field>
<!--      <v-card flat v-for="(item, i) in searchItems" :key="i" v-model="searchItems">-->
<!--        <v-card-title>{{item.name}}</v-card-title>-->
<!--      </v-card>-->
    </v-card>
    <v-card style="margin: 2% 5% 0 5%" flat>
      <v-card-title>Доступные машины:</v-card-title>
      <MachineList :machines="info.machines"/>
    </v-card>
  </v-app>
</template>

<script>
import MachineList from "@/components/MachineList";
const axios = require('axios')

export default {
  name: "Home",
  components: {
    MachineList
  },
  data() {
    return {
      info: {
        user_info: null,
        machines: null
      },
      searchVal: '',
      searchItems: null
    }
  },
  methods: {
    searchMachine() {
      if (this.searchVal !== '') {
        axios.get('http://192.168.0.113:8000/api/machines')
            .then(resp => (this.info.machines = resp.data['machines'].filter(i => new RegExp(this.searchVal.toLowerCase()).test(i.name.toLowerCase()))))
      } else {
        axios.get('http://192.168.0.113:8000/api/machines')
            .then(resp => (this.info.machines = resp.data['machines']))
      }
    }
  },
  mounted() {
    axios.get('http://192.168.0.113:8000/api/users')
        .then(resp => (this.info.user_info = resp.data['users']))
    axios.get('http://192.168.0.113:8000/api/machines')
      .then(resp => (this.info.machines = resp.data['machines']))
  }
}
</script>

<style scoped>

</style>