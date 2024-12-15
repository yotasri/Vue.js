<template>
  <v-app>
    <!-- App Bar -->
    <v-app-bar color="#2273cf" elevation="4" class="app-bar">
      <template v-slot:prepend>
        <div class="rounded-circle d-flex justify-center align-center" style="background-color: white; width: 40px; height: 40px;">
          <v-icon color="teal" size="24">mdi-arrow-left</v-icon>
        </div>
      </template>
      <v-app-bar-title>Student List</v-app-bar-title>
      <v-spacer></v-spacer>
      <v-menu offset-y>
        <template v-slot:activator="{ props }">
          <v-btn v-bind="props" variant="text" class="sort-btn">
            Sort by time <v-icon>mdi-chevron-down</v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item value="newest">
            <v-list-item-title>Newest First</v-list-item-title>
          </v-list-item>
          <v-list-item value="oldest">
            <v-list-item-title>Oldest First</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>

    <!-- Main Content -->
    <v-main class="bg-grey-lighten-3">
      <v-container class="px-2">
        <div class="text-subtitle-2 text-grey-darken-1 mb-4">
          E1 เทคโนโลยีสารสนเทศ<br>
          ครูที่ปรึกษา : ครูกฤษณุา แนววิเศษ
        </div>

        <!-- Layout Change: Grid View -->
        <v-row>
          <v-col v-for="(item, index) in activities" :key="index" cols="12" sm="6" md="4" lg="3">
            <v-card class="activity-card">
              <v-card-title>
                <v-avatar size="48" color="info" class="mr-3">
                  <v-img :src="'http://localhost:7000/uploads/profile/' + item.picture" max-width="100px" max-height="100px"></v-img>
                </v-avatar>
                {{ item.username }}
              </v-card-title>
              <v-card-subtitle class="text-grey-darken-1">
                {{ item.student_id }}
              </v-card-subtitle>
              <v-card-actions>
                <v-menu offset-y>
                  <template v-slot:activator="{ props }">
                    <v-icon v-bind="props" size="18" class="cursor-pointer">
                      mdi-dots-horizontal
                    </v-icon>
                  </template>
                  <v-list>
                    <v-list-item v-for="(option, i) in attendanceOptions" :key="i" @click="updateAttendance(index, option.value)">
                      <v-list-item-title>
                        <v-btn class="attendance-btn" :color="option.color" outlined>
                          {{ option.text }}
                        </v-btn>
                      </v-list-item-title>
                    </v-list-item>
                  </v-list>
                </v-menu>
                <div class="text-grey-darken-1 text-caption mb-1">
                  <span v-if="item.attendance !== undefined" class="ml-2">({{ item.attendance }})</span>
                </div>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>

        <v-btn @click="submitAttendance" color="primary" class="mt-4 submit-btn">
          Submit Attendance
        </v-btn>
      </v-container>
    </v-main>

    <!-- Bottom Navigation -->
    <v-bottom-navigation grow color="#2273cf" class="bottom-nav">
      <v-btn value="home" class="nav-btn" @click="home">
        <v-icon>mdi-home</v-icon>
        Home
      </v-btn>
      <v-btn value="chat" class="nav-btn">
        <v-icon>mdi-chat</v-icon>
        Chat
      </v-btn>
      <v-btn value="mail" class="nav-btn">
        <v-icon>mdi-email</v-icon>
        Mail
      </v-btn>
      <v-btn value="profile" class="nav-btn">
        <v-icon>mdi-account</v-icon>
        Profile
      </v-btn>
    </v-bottom-navigation>
  </v-app>
</template>

<script setup>
import axios from "axios"
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router';

const router = useRouter();
function home() {
  router.push('/student_profile');
}

const attendanceOptions = [
  { text: 'ขาด', value: 0, color: 'red' },
  { text: 'ลา', value: 0.5, color: 'amber' },
  { text: 'มา', value: 1, color: 'green' },
  { text: 'สาย', value: 0.5, color: 'yellow' }
]

const activities = ref([])

// Function to fetch data from the API
const listData = async () => {
  try {
    const response = await axios.get("http://localhost:7000/listStudent");
    activities.value = response.data.datas;
  } catch (error) {
    console.error('Error fetching data:', error);
  }
}

onMounted(() => {
  listData();
})

const updateAttendance = (index, value) => {
  activities.value[index] = {
    ...activities.value[index],
    attendance: value
  }
}

const submitAttendance = async () => {
  const payload = activities.value.map(activity => ({
    studentName: activity.username,
    studentId: activity.student_id,
    attendance: activity.attendance,
    password: activity.password,
    picture: activity.picture
  }))
  
  try {
    const response = await axios.post("http://localhost:7000/insert1", { activities: payload });
    console.log('API Response:', response.data);
  } catch (error) {
    console.error('Error sending data to API:', error);
  }
}
</script>

<style scoped>
.app-bar {
  border-bottom: 2px solid #29b79f;
}

.sort-btn {
  color: #ffffff;
}

.activity-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.activity-card:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
  cursor: pointer;
}

.submit-btn {
  border-radius: 8px;
  font-size: 1.1rem;
  font-weight: bold;
}

.bottom-nav {
  border-top: 2px solid #2988b7;
  background-color: #2273cf;
}

.nav-btn {
  font-weight: bold;
  color: #ffffff;
  background-color: transparent;
}

.nav-btn:hover {
  background-color: #4cb7f5;
}

.attendance-btn {
  font-weight: bold;
  text-transform: none;
  padding: 6px 12px;
  font-size: 0.9rem;
  margin: 5px;
}

.attendance-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.attendance-btn:focus {
  outline: none;
}
</style>