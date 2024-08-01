<template>
    <div>
      <h1>Hello {{ name }}</h1>
      <input type="text" :value="name">
    </div>
  </template>
  <script>
  export default {
    data() {
      return {
        name: "World CTC",
      }
    },
  }
  </script>