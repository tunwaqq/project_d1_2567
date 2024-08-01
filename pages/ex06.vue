<template>
    <div>
      <h1>Hello {{ name }}</h1>
      <div>{{ upperName }}</div>
      <input type="text" :value="name"><br>
      <input type="text" v-model="name"><br>
    </div>
  </template>
<script>
export default {
  data() {
    return { name: "World Chaiyaphum" }
  },
  computed: {
    //ตัวเรียกใช้
    upperName() {
      return this.name.toUpperCase()
    }
  }
}
</script>