<template>
    <v-card
      title="Nutrition"
      flat
    >
      <template v-slot:text>
        <v-text-field
          v-model="search"
          label="Search"
          prepend-inner-icon="mdi-magnify"
          variant="outlined"
          hide-details
          single-line
        ></v-text-field>
      </template>
  
      <v-data-table
        :headers="headers"
        :items="students"
        :search="search"
      ></v-data-table>
    </v-card>
  </template>

<script>
import axios from "axios";
export default {
  data () {
    return {
      search: '',
      headers: [
        {
          align: 'start',
          key: 'id',
          sortable: false,
          title: 'id',
        },
        { key: 'username', title: 'Username' },
        { key: 'password', title: 'Password' },
        { key: 'email', title: 'email ' },
        { key: 'status', title: 'status' },
        { key: 'picture', title: 'Image ' },
      ],
      students: [
        {
          name: 'Frozen Yogurt',
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
          iron: 1,
        },
        
      ],
    }
  },
  async created() {
    console.log('init list data');
    const response = await axios.get('http://localhost:7000/list')
    this.students = response.data.rows
  },
}
</script>