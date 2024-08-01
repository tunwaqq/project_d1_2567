<template>
    <v-data-table
      :headers="headers"
      :items="desserts"
      :search="search"
      :sort-by="[{ key: 'calories', order: 'asc' }]"
    >
      <template v-slot:top>
        <v-toolbar
          flat
        >
          <v-toolbar-title>{{ member }}</v-toolbar-title>
          <v-divider
            class="mx-4"
            inset
            vertical
          ></v-divider>
          <v-text-field
          v-model="search"
          label="Search"
          prepend-inner-icon="mdi-magnify"
          variant="outlined"
          hide-details
          single-line
        ></v-text-field>
          <v-spacer></v-spacer>
          <v-dialog
            v-model="dialog"
            max-width="800px"
          >
            <template v-slot:activator="{ props }">
              <v-btn
                class="mb-2"
                color="primary"
                dark
                v-bind="props"
              >
                เพิ่มข้อมูล
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>
  
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col
                      cols="12"
                      md="4"
                      sm="6"
                    >
                      <v-text-field
                        v-model="editedItem.id"
                        label="Id"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      md="4"
                      sm="6"
                    >
                      <v-text-field
                        v-model="editedItem.username"
                        label="username"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      md="4"
                      sm="6"
                    >
                      <v-text-field
                        v-model="editedItem.password"
                        label="password"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      md="4"
                      sm="6"
                    >
                      <v-text-field
                        v-model="editedItem.email"
                        label="E-mail"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      md="4"
                      sm="6"
                    >
                      <v-text-field
                        v-model="editedItem.status"
                        label="status"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      md="4"
                      sm="6"
                    >
                      <v-text-field
                        v-model="editedItem.picture"
                        label="Picture"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
  
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  color="blue-darken-1"
                  variant="text"
                  @click="close"
                >
                  Cancel
                </v-btn>
                <v-btn
                  color="blue-darken-1"
                  variant="text"
                  @click="save"
                >
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue-darken-1" variant="text" @click="closeDelete">Cancel</v-btn>
                <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">OK</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon
          class="me-2"
          size="small"
          @click="editItem(item)"
        >
          mdi-pencil
        </v-icon>
        <v-icon
          size="small"
          @click="deleteItem(item)"
        >
          mdi-delete
        </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn
          color="primary"
          @click="initialize"
        >
          Reset
        </v-btn>
      </template>
    </v-data-table>
  </template>
  
  <script>
  import axios from 'axios'
  import Index from './index.vue';

    export default {
      data: () => ({
        search:'',
        member: 'รายชื่อสมาชิก',
        id: '',
        dialog: false,
        dialogDelete: false,
        headers: [
          {title: 'ID', align: 'start', sortable: false,  key: 'id', },
          { title: 'Username', key: 'username' },
          { title: 'Password', key: 'password' },
          { title: 'E-mail', key: 'email' },
          { title: 'Status', key: 'status' },
          { title: 'Picture', key: 'picture' },
          { title: 'Actions', key: 'actions', sortable: false },
        ],
        desserts: [],
        editedIndex: -1,
        editedItem: {},
        defaultItem: {
          name: '',
          calories: 0,
          fat: 0,
          carbs: 0,
          protein: 0,
        },
      }),
  
      computed: {
        formTitle () {
          return this.editedIndex === -1 ? 'เพิ่มสมาชิก' : 'แก้ไขสมาชิก'
        },
      },
  
      watch: {
        dialog (val) {
          val || this.close()
        },
        dialogDelete (val) {
          val || this.closeDelete()
        },
      },


  
      async created () {
        console.log('init list data');

        const response = await axios.get('http://localhost:7000/list')
        this.desserts = response.data.rows
        
        // this.initialize()
      },

      mounted() {
        let user = window.sessionStorage.getItem('email');
        console.log('user=' , user);
        if (!user){  // user == null
        this.$router.replace('/login') 
    }
      },  

      methods: {
        
        initialize () {},
        editItem (item) {
          this.editedIndex = this.desserts.indexOf(item)
          this.editedItem = Object.assign({}, item)
          this.dialog = true
        },
  
        deleteItem (item) { 
         this.id = item.id        
        console.log(item.id)
          this.dialogDelete = true
        },
  
        async deleteItemConfirm () {
          console.log('confirm')
          console.log(this.id)
          const response = await axios.get('http://localhost:7000/delete?id='+ this.id)
          this.desserts = response.data.rows
          this.closeDelete()
        },
  
        close () {
          this.dialog = false
          this.$nextTick(() => {
            this.editedItem = Object.assign({}, this.defaultItem)
            this.editedIndex = -1
          })
        },
  
        closeDelete () {
          this.dialogDelete = false
          this.$nextTick(() => {
            this.editedItem = Object.assign({}, this.defaultItem)
            this.editedIndex = -1
          })
        },
  
        async save () {
          let insert;

          if(this.editedIndex === -1){ 
            console.log(this.editedItem)
             insert = await axios.post('http://localhost:7000/insert', this.editedItem)
            this.students = insert.data.rows
            this.close()
          } else {
            let update;

            console.log(this.editedItem)
             update = await axios.post('http://localhost:7000/update', this.editedItem)
            this.students = update.data.rows
            this.close()
          }
        },
      },
    }
  </script>