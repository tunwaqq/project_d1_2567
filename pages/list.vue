<template>
    <v-data-table
      :headers="headers"
      :items="students"
      :sort-by="[{ key: 'username', order: 'asc' }]"
    >
      <template v-slot:top>
        <v-toolbar
          flat
        >
          <v-toolbar-title>My CRUD</v-toolbar-title>
          <v-divider
            class="mx-4"
            inset
            vertical
          ></v-divider>
          <v-spacer></v-spacer>
          <v-dialog
            v-model="dialog"
            max-width="500px"
          >
            <template v-slot:activator="{ props }">
              <v-btn
                class="mb-2"
                color="primary"
                dark
                v-bind="props"
              >
                New Item
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
                        label="id"
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
                        label="email"
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
                        label="picture"
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
  export default {
    data: () => ({
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          title: 'id',
          align: 'start',
          sortable: false,
          key: 'id',
        },
        { title: 'username', key: 'username' },
        { title: 'password', key: 'password' },
        { title: 'email', key: 'email' },
        { title: 'status', key: 'status' },
        { title: 'picture', key: 'picture' },
        { title: 'Actions', key: 'actions', sortable: false },
      ],
      students: [],
      editedIndex: -1,
      editedItem: {
        id: '',
        username: 0,
        email: 0,
        status: 0,
        password: 0,
        picture: 0,
      },
      defaultItem: {
        id: '',
        username: 0,
        email: 0,
        status: 0,
        password: 0
      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'เพิ่มข้อมูลใหม่' : 'แก้ไขข้อมูล'
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
      this.initialize()
      console.log('init list data');
      const response = await axios.get('http://localhost:7000/list')
      this.students = response.data.rows
    },

    methods: {
      initialize () {
        this.students = [
          {
            id: 'Frozen Yogurt',
            username: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
          },
          {
            id: 'Ice cream sandwich',
            username: 237,
            fat: 9.0,
            carbs: 37,
            protein: 4.3,
          },
          
        ]
      },

       editItem (item) {
        console.log('item=',item)
        this.editedIndex = this.students.indexOf(item)
        console.log('this.editedIndex=',  this.editedIndex)
        this.editedItem = Object.assign({}, item)
        console.log('this.editedItem =',this.editedItem )
        this.dialog = true
 
      },

      deleteItem (item) {
        this.editedIndex = this.students.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        this.students.splice(this.editedIndex, 1)
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
        console.log('save data', Object.assign(this.students[this.editedIndex], this.editedItem))
        let stds = Object.assign(this.students[this.editedIndex], this.editedItem)
          const response = await axios.post('http://localhost:7000/update',stds)
          this.stds = response.data
          console.log("stds=",this.stds)
        this.close()
      },
    },
  }
</script>