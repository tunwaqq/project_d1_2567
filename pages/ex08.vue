<template>
    <div>
      <h1>Student List {{ student[0].name }}</h1>
      <!-- <ul>
        <li v-for="st in student" :key="st.code">
          <span>{{ st.code }}</span>
          <span>{{ st.name }}</span>
        </li>
      </ul> -->
    </div>
  </template>
  <script>
  export default {
    data() {
      return {
        student: [
        { code: '001', name: 'Name1' },
          { code: '002', name: 'Name2' },
          { code: '003', name: 'Name3' },
          { code: '003', name: 'Name4' },
        ],
      }
    },
  }
  </script>