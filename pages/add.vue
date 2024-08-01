<template>
  <div class="app-container">
    <div class="text-center pa-4">
      <v-dialog
        v-model="dialog"
        width="auto"
      >
        <v-card
          max-width="400"
          prepend-icon="mdi-update"
          text="บันทึกสำเร็จ"
          title="สถานะการบันทึก"
        >
          <template v-slot:actions>
            <v-btn
              class="ms-auto"
              text="Ok"
              @click="dialog = false"
            ></v-btn>
          </template>
        </v-card>
      </v-dialog>
    </div>

    <div class="text-center pa-4">
      <v-dialog
        v-model="dialogerror"
        width="auto"
      >
        <v-card
          max-width="400"
          prepend-icon="mdi-update"
          text="บันทึกไม่สำเร็จ"
          title="สถานะการบันทึก"
        >
          <template v-slot:actions>
            <v-btn
              class="ms-auto"
              text="Ok"
              @click="dialogerror = false"
            ></v-btn>
          </template>
        </v-card>
      </v-dialog>
    </div>

    <v-sheet class="form-container mx-auto">
      <v-form ref="form">
        <v-text-field
          v-model="username"
          label="UserName"
          required
        ></v-text-field>
        <v-text-field
          v-model="password"
          label="Password"
          required
          type="password"
        ></v-text-field>
        <v-text-field
          v-model="email"
          label="Email"
          required
          type="email"
        ></v-text-field>
        <v-text-field
          v-model="picture"
          label="Picture"
          required
        ></v-text-field>
        <v-select
          v-model="status"
          :items="items"
          :rules="[v => !!v || 'Item is required']"
          label="Status"
          required
        ></v-select>

        <div class="d-flex flex-column">
          <v-btn
            class="mt-4"
            color="success"
            block
            @click="save"
          >
            SAVE
          </v-btn>
          <v-btn
            class="mt-4"
            color="error"
            block
            @click="reset"
          >
            Reset Form
          </v-btn>
        </div>
      </v-form>
    </v-sheet>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data: () => ({
    status_save: '',
    dialog: false,
    dialogerror: false,
    std: '',
    username: '',
    password: '',
    email: '',
    picture: '',
    status: null,
    items: ['Yes', 'No'],
  }),

  methods: {
    async save() {
      let students = {
        username: this.username,
        password: this.password,
        email: this.email,
        status: this.status,
        picture: this.picture,
      };
      console.log(students);
      const response = await axios.post('http://localhost:7000/insert', students);
      this.std = response.data;
      console.log('std =', this.std);
      if (this.std.status == 'ok') {
        this.dialog = true;
        console.log('สำเร็จ');
        this.status_save = 'บันทึกสำเร็จ';
      } else {
        this.dialogerror = true;
        console.log('error:');
        this.status_save = 'บันทึกไม่สำเร็จ';
      }
    },
    reset() {
      this.$refs.form.reset();
    },
  },
};
</script>

<style scoped>
.app-container {
  background-image: url('/assets/images/anime-girl-white-hair-cyberpunk-sci-fi--2k-wallpaper-uhdpaper.com-198@0@k.jpg');
  background-size: cover;
  background-attachment: fixed;
  padding: 20px;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.form-container {
  background-color: rgba(255, 255, 255, 0.9); /* Slightly more opaque */
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  width: 400px; /* Adjust the width as needed */
  min-height: 500px; /* Adjust the height to make the form container longer */
}
</style>
