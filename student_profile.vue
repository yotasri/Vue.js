<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card class="elevation-2 mb-4">
          <v-list>
            <v-list-item>
              <v-img :src="userPicture" alt="Student Picture" width="100%" height="100%" contain></v-img>
              <v-list-item-title>
                Welcome to Student Profile
                <p>ID: {{ studentId }}</p>
                <p>Username: {{ username }}</p>
              </v-list-item-title>
            </v-list-item>
          </v-list>
        </v-card>

        <v-card class="pa-4 elevation-2">
          <div class="text-h5 font-weight-bold mb-3">Quick Actions</div>
          <!-- เพิ่มปุ่มไปยังหน้าอื่น ๆ -->
          <v-btn block color="info" class="mt-4" @click="goToDashboard">Student Check</v-btn>
          <v-btn block color="success" class="mt-2" @click="goToSettings">Member List</v-btn>
          <v-btn block color="red" class="mt-2" @click="logout">LOGOUT</v-btn>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

export default {
  setup() {
    const username = ref('');
    const email = ref('');
    const studentId = ref('');
    const selectedImage = ref('');
    const errorMessage = ref('');
    const router = useRouter();

    onMounted(() => {
      // ตรวจสอบว่า localStorage รองรับ
      if (typeof window !== 'undefined' && typeof localStorage !== 'undefined') {
        // เช็คข้อมูลจาก localStorage
        const storedUsername = localStorage.getItem('username');
        const storedStudentId = localStorage.getItem('studentId');
        const storedPicture = localStorage.getItem('profilePicture');

        if (storedUsername) username.value = storedUsername;
        if (storedStudentId) studentId.value = storedStudentId;
        if (storedPicture) selectedImage.value = storedPicture;

        if (!storedStudentId && username.value) {
          axios
            .get('http://localhost:7000/user-profile', {
              params: { username: username.value },
              headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`,
              },
            })
            .then((response) => {
              email.value = response.data.email;
              studentId.value = response.data.student_id;
              selectedImage.value = response.data.picture || '/default-avatar.png';

              // เก็บข้อมูลใน localStorage
              localStorage.setItem('studentId', studentId.value);
              localStorage.setItem('profilePicture', selectedImage.value);
            })
            .catch((error) => {
              errorMessage.value = 'ไม่สามารถดึงข้อมูลได้';
            });
        }
      }
    });

    // ฟังก์ชันสำหรับไปหน้า Dashboard
    function goToDashboard() {
      router.push('/student_check');
    }

    // ฟังก์ชันสำหรับไปหน้า Settings
    function goToSettings() {
      router.push('/showStudent');
    }

    // ฟังก์ชัน Logout
    function logout() {
      localStorage.clear();
      router.push('/login');
    }

    return {
      username,
      studentId,
      selectedImage,
      errorMessage,
      goToDashboard,
      goToSettings,
      logout,
      userPicture: selectedImage,
    };
  },

  mounted() {
    const token = localStorage.getItem('token');
    // ตรวจสอบว่ามี Token หรือไม่
    if (!token) {
      this.$router.push('/login'); // ถ้าไม่มีให้กลับไปที่หน้า login
    } else {
      try {
        const response = axios.get('http://localhost:7000/checktoken', {
          headers: { Authorization: `Bearer ${token}` },
        });
      } catch (error) {
        console.error('Error:', error.response.data);
      }
    }
  },
};
</script>

<style scoped>
.profile-section {
  margin-top: 2rem;
}

.text-h6 {
  font-size: 1.25rem;
}

.v-main {
  padding-top: calc(25vh + 16px);
  background-color: #f8f9fa;
}

.v-card {
  border-radius: 8px;
}

.v-btn {
  border-radius: 25px;
  text-transform: uppercase;
  font-weight: bold;
}
</style>
