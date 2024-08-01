<template>
    <div class="profile-card">
      <img :src="profile.image" alt="Profile Image" class="profile-image"/>
      <h2>{{ profile.firstName }} {{ profile.lastName }}</h2>
      <p class="address">{{ profile.address }}</p>
      <p class="phone">{{ profile.phone }}</p>
    </div>
  </template>
  
  <script>
  export default {
    name: 'ProfileCard',
    data() {
      return {
        profile: {
          image: '/assets/images/5f327049-521a-41c0-b4ec-37285f33405c.jpg', // เปลี่ยน URL ให้เป็นรูปจริง
          firstName: 'นาย ปุญญาพัฒน์ ',
          lastName: 'โพธิ์งาม',
          address: 'ที่อยู่ 305/2 หมู่ 3 บ้านหนองบัวบาน อ.จัตุรัส จ.ชัยภูมิ ',
          phone: '082-804-8893',
        }
      }
    }
  }
  </script>
  
  <style scoped>
  .profile-card {
    border: 1px solid #e0e0e0;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    padding: 30px;
    text-align: center;
    width: 600px;
    height: 500px;
    margin: 20px auto;
    background-color: #fff;
    border-radius: 10px;
    font-family: 'Arial', sans-serif;
    background-image: url('/assets/images/anime-girl-white-hair-cyberpunk-sci-fi--2k-wallpaper-uhdpaper.com-198@0@k.jpg'); /* เพิ่ม URL ของรูปพื้นหลัง */
    background-size: cover; /* ปรับขนาดรูปให้ครอบคลุมทั้งกรอบ */
    background-position: center; /* จัดตำแหน่งรูปให้อยู่ตรงกลาง */
  }
  
  .profile-image {
    width: 200px;
    height: 200px;
    border-radius: 20%;
    object-fit: cover;
    margin-bottom: 20px;
    border: 2px solid #ccc;
  }
  
  h2 {
    margin: 10px 0;
    font-size: 28px;
    color: #ffffff;
  }
  
  .address, .phone {
    margin: 5px 0;
    font-size: 18px;
    color: #ffffff;
  }
  </style>
  