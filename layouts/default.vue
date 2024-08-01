<template>
  <v-app>
    <v-card>
      <v-layout>
        ///
        
        ///
        <v-app-bar
          color="teal-darken-4"
          image="https://atd-blog.s3.us-east-2.amazonaws.com/wp-content/uploads/2022/07/17140324/1-design-zoom-background.webp"
        >
          <template v-slot:image>
            <v-img
              
            ></v-img>
          </template>

          <template v-slot:prepend>
            <v-app-bar-nav-icon></v-app-bar-nav-icon>
          </template>
          <v-app-bar-title>Title</v-app-bar-title>
          <v-spacer></v-spacer>
          <v-btn icon>
            <v-icon>mdi-magnify</v-icon>
          </v-btn>
          <v-btn icon>
            <v-icon>mdi-heart</v-icon>
          </v-btn>
          <v-btn icon>
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </v-app-bar>
        <v-navigation-drawer 
          class="bg-black"
          v-model="drawer"
          :rail="rail"
          permanent
          @click="rail = false"
        >
          <v-list-item
            prepend-avatar="\assets\images\Profile1.jpg"
            title="John Leider"
            nav
          >
            <template v-slot:append>
              <v-btn
                icon="mdi-chevron-left"
                variant="text"
                @click.stop="rail = !rail"
              ></v-btn>
            </template>
          </v-list-item>
          <v-divider></v-divider>
          <v-list density="compact" nav>
            <v-list-item
            color="error"
              prepend-icon="mdi-home-city"
              title="หน้าหลัก"
              value="home"
              @click="main"
            ></v-list-item>
            <v-list-item
           
              prepend-icon="mdi-plus"
              color="error"
              size="x-large"
              title="เพิ่มข้อมูล"
              value="add"
              @click="add"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-home-city"
              color="error"
              title="แสดงข้อมูล"
              value="city"
              @click="listdata"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-account"
              color="error"
              title="เกี่ยวกับ"
              value="account"
              @click="about"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-close-circle"
              color="error"
              title="LOGOUT"
              value="users"
              @click="logOut"
            ></v-list-item>
          </v-list>
        </v-navigation-drawer>
        <!-- <v-main style="height: 650px"></v-main> -->
        <v-main >
         <NuxtPage></NuxtPage>
          <!-- Main Content -->
        </v-main>
        <!--  -->

        <v-bottom-navigation v-model="value" :bg-color="color" mode="shift" >
          <v-btn>
            <v-icon>mdi-television-play</v-icon>

            <span>Video</span>
          </v-btn>

          <v-btn>
            <v-icon>mdi-music-note</v-icon>

            <span>Music</span>
          </v-btn>

          <v-btn>
            <v-icon>mdi-book</v-icon>

            <span>Book</span>
          </v-btn>

          <v-btn>
            <v-icon>mdi-image</v-icon>

            <span>Image</span>
          </v-btn>
        </v-bottom-navigation>
      </v-layout>
    </v-card>
  </v-app>
</template>
<script>
import axios from 'axios'

export default {
  data() {
    return {
      drawer: true,
      rail: true,
      email: ''
    };
  },
  async mounted() {
    let user = window.sessionStorage.getItem("email");
    console.log("user=", user);
    if (!user){
      this.$router.replace('/login')
      return
    }
  },
  methods: {
    main(){
      this.$router.replace("/home");
    },
    listdata(){
      this.$router.replace("/showdata");
    },
    showdata(){
      this.$router.replace("/");
    },
    about(){
      this.$router.replace("/aboute");
    },
    add(){
      this.$router.replace("/add");
    },
    logOut() {
      window.sessionStorage.clear();
      this.$router.replace("/login");
    },
  },
};
</script>