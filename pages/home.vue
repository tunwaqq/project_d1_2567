<template>
  <v-responsive class="border rounded" max-height="auto">
    <v-app>
      <v-app-bar :elevation="2">
        <template v-slot:prepend>
          <v-app-bar-nav-icon></v-app-bar-nav-icon>
        </template>

        <v-app-bar-title> Name: {{ username }} &nbsp; E-mail: {{ email }}</v-app-bar-title>
        <div class="text-center">
          <v-btn append-icon="mdi-account-circle" prepend-icon="mdi-check-circle" @click="logOut">
            <template v-slot:prepend>
              <v-icon color="success"></v-icon>
            </template>
            Logout
            <template v-slot:append>
              <v-icon color="warning"></v-icon>
            </template>
          </v-btn>
        </div>
      </v-app-bar>

      <v-carousel>
        <v-carousel-item v-for="(item, i) in items" :key="i">
          <div class="carousel-image-container">
            <v-img :src="item.src" :height="frameHeight + 'px'" class="carousel-image">
              <v-row class="fill-height" align="center" justify="center">
                <v-col class="text-center" cols="12">
                  <h1 class="display-1 white--text">{{ item.title }}</h1>
                </v-col>
              </v-row>
            </v-img>
          </div>
        </v-carousel-item>
      </v-carousel>

      <v-data-iterator :items="desserts" item-value="name">
        <template v-slot:default="{ items, isExpanded, toggleExpand }">
          <v-row>
            <v-col v-for="item in items" :key="item.name" cols="12" md="6" sm="12">
              <v-card>
                <v-card-title class="d-flex align-center">
                  <v-icon :color="item.color" :icon="item.icon" size="18" start></v-icon>
                  <h4>{{ item.name }}</h4>
                </v-card-title>

                <v-card-text>
                  {{ item.description }}
                </v-card-text>

                <div class="px-4">
                  <v-switch :label="`${isExpanded(item) ? 'Hide' : 'Show'} details`" :model-value="isExpanded(item)" density="compact" inset @click="() => toggleExpand(item)"></v-switch>
                </div>

                <v-divider></v-divider>

                <v-expand-transition>
                  <div v-if="isExpanded(item)">
                    <v-list :lines="false" density="compact">
                      <v-list-item :title="`🔥 Calories: ${item.calories}`" active></v-list-item>
                      <v-list-item :title="`🍔 Fat: ${item.fat}`"></v-list-item>
                      <v-list-item :title="`🍞 Carbs: ${item.carbs}`"></v-list-item>
                      <v-list-item :title="`🍗 Protein: ${item.protein}`"></v-list-item>
                      <v-list-item :title="`🧂 Sodium: ${item.sodium}`"></v-list-item>
                      <v-list-item :title="`🦴 Calcium: ${item.calcium}`"></v-list-item>
                      <v-list-item :title="`🧲 Iron: ${item.iron}`"></v-list-item>
                    </v-list>
                  </div>
                </v-expand-transition>
              </v-card>
            </v-col>
          </v-row>
        </template>
      </v-data-iterator>
    </v-app>
  </v-responsive>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      username: '',
      email: '',
      items: [
        {
          src: 'assets/images/yinlin___wuthering_waves_by_izumi2630_dhlfckt-fullview.jpg',
          title: ''
        },
        {
          src: 'https://www.goha.ru/s/f/Nj/Py/evVu1NPICy.jpg',
          title: ''
        },
        {
          src: 'https://www.lapakgaming.com/blog/en-sg/wp-content/uploads/2024/05/Untitled.jpg',
          title: ''
        }
      ],
      desserts: [
        {
          name: 'Frozen Yogurt',
          description: 'A tangy and creamy dessert made from yogurt and sometimes fruit, Frozen Yogurt is a lighter alternative to ice cream. Perfect for those who crave a sweet treat but want to keep it on the healthier side.',
          icon: 'mdi-ice-cream',
          color: '#6EC1E4',
          calories: 159,
          fat: 6,
          carbs: 24,
          protein: 4,
          sodium: 87,
          calcium: '14%',
          iron: '1%',
        },
        {
          name: 'Ice Cream Sandwich',
          description: 'A classic treat featuring a layer of creamy ice cream sandwiched between two cookies or cake layers. Ideal for those hot summer days when you can\'t decide between a cookie and ice cream.',
          icon: 'mdi-cookie',
          color: '#F4A261',
          calories: 237,
          fat: 9,
          carbs: 37,
          protein: 4.3,
          sodium: 129,
          calcium: '8%',
          iron: '1%',
        },
        {
          name: 'Eclair',
          description: 'A small, individual cake topped with frosting and often adorned with sprinkles or other decorations. Great for parties or as a quick indulgence when you need a sugar fix.',
          icon: 'mdi-cake-variant',
          color: '#6D4C41',
          calories: 262,
          fat: 16,
          carbs: 23,
          protein: 6,
          sodium: 337,
          calcium: '6%',
          iron: '7%',
        },
        {
          name: 'Cupcake',
          description: 'A small, individual cake topped with frosting and often adorned with sprinkles or other decorations. Great for parties or as a quick indulgence when you need a sugar fix.',
          color: '#FFADAD',
          icon: 'mdi-cupcake',
          calories: 305,
          fat: 3.7,
          carbs: 67,
          protein: 4.3,
          sodium: 413,
          calcium: '3%',
          iron: '8%',
        },
      ],
    };
  },
  
  async mounted() {
    const user = window.sessionStorage.getItem('email');
    if (user) {
      try {
        const response = await axios.get(`http://localhost:7000/listid?email=${user}`);
        const data = response.data;
        this.email = data.row.email;
        this.username = data.row.username;
      } catch (error) {
        console.error('Error fetching user data:', error);
      }
    }
  },
  methods: {
    logOut() {
      window.sessionStorage.clear();
      this.$router.replace('/login');
    },
  },
};
</script>

<style>
.carousel-image-container {
  position: relative;
  overflow: hidden;
  width: 100%;
  height: 1000px;
}

.carousel-image {
  object-fit: cover;
}

.v-carousel-item {
  height: auto;
}

.v-img {
  width: 100%;
}
</style>
