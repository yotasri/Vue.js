<template>
  <v-app class="app-container">
    <!-- Gradient Header -->
    <v-card class="gradient-card custom-rounded-bottom" height="200px" flat></v-card>

    <!-- App Bar -->
    <v-app-bar app color="transparent" elevation="0" class="app-bar" fixed>
      <v-spacer></v-spacer>
      <v-btn icon color="white" @click="closeForm">
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </v-app-bar>

    <!-- Welcome Text -->
    <div class="welcome-text center-text">
      <div class="bold-text">Registration</div>
    </div>

    <!-- Form -->
  <v-main class="main-content">
    <v-container fill-height>
      <v-row justify="center" align="center">
        <v-col cols="12" sm="8" md="6" class="form-container">
          <v-form ref="form" v-model="valid">
          <!-- Student ID -->
          <v-text-field
            v-model="student_id"
            :rules="nameRules"
            label="Student ID"
            required
            class="custom-text-field"
          >
            <template v-slot:prepend-inner>
              <v-icon color="#4c8479">mdi-account</v-icon>
            </template>
          </v-text-field>

          <!-- Email -->
          <v-text-field
            v-model="email"
            :rules="emailRules"
            label="Email Address"
            required
            class="custom-text-field"
          >
            <template v-slot:prepend-inner>
              <v-icon color="#4c8479">mdi-email</v-icon>
            </template>
          </v-text-field>

          <!-- Password -->
          <v-text-field
            v-model="password"
            :rules="passwordRules"
            label="Password"
            :type="showPassword ? 'text' : 'password'"
            required
            class="custom-text-field"
          >
            <template v-slot:prepend-inner>
              <v-icon color="#4c8479">mdi-lock</v-icon>
            </template>
            <template v-slot:append>
              <v-icon @click="showPassword = !showPassword" color="#4c8479">
                {{ showPassword ? 'mdi-eye' : 'mdi-eye-off' }}
              </v-icon>
            </template>
          </v-text-field>

          <!-- Sign Up Button -->
          <v-btn
            block
            @click="signUp"
            class="login-btn gradient-btn mt-4"
            height="50"
          >
            Sign Up
          </v-btn>

          <!-- Sign In Link -->
          <div class="text-center mt-4">
            Already have an account?
            <router-link to="/signin" class="create-account-link">
              Sign In
            </router-link>
              </div>
            </v-form>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>




<script>
import axios from 'axios'
import { useRouter } from 'vue-router';

export default {
  name: 'RegistrationForm',
  data: () => ({
    dialog: false,
    valid: true,
    student_id: '',
    email: '',
    password: '',
    showPassword: false,
    emailRules: [
      v => !!v || 'E-mail is required',
      v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
    ],
    passwordRules: [
      v => !!v || 'Password is required',
      v => v.length >= 8 || 'Password must be at least 8 characters',
    ],
  }),
  setup() {
    const router = useRouter();
    return { router };
  },
  methods: {
    async signUp() {
      const student = {
        student_id: this.student_id,
        username: this.email,
        password: this.password,
      };

      if (this.$refs.form.validate()) {
        try {
          const response = await axios.post('http://localhost:7000/insertStudent', student);
          const status = response.data.ok;

          if (status === 0) {
            console.log('Error inserting data');
            this.dialog = true;
          } else {
            console.log('Insert successful');
            this.router.push('/login');
          }
        } catch (error) {
          console.error('Error:', error.message);
        }
      } else {
        console.log('Please fill all required fields correctly');
      }
    },
  },
};
</script>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  overflow: hidden;
  background: linear-gradient(90deg, #2273cf, #ffffff);
}

.gradient-card {
  background: linear-gradient(90deg, #2273cf 0%, #ffffff 100%);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 0;
}

.custom-rounded-bottom {
  border-bottom-left-radius: 40px !important;
  border-bottom-right-radius: 40px !important;
}

.welcome-text {
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  top: 50px; /* ปรับระยะห่างจากขอบบน */
  left: 0;
  right: 0;
  height: 150px; /* ลดความสูงของพื้นที่ข้อความ */
  z-index: 1;
}

.normal-text,
.bold-text {
  font-size: 30px;
  font-weight: bold;
}

.bold-text {
  font-weight: bold;
}

.main-content {
  display: flex;
  justify-content: center;
  align-items: center;
  flex: 1;
  background: linear-gradient(90deg, #2273cf, #ffffff);
  padding: 20px;
}

.form-container {
  background: #ffffff;
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 500px; /* จำกัดความกว้างของฟอร์ม */
  margin-top: 100px; /* เพิ่มระยะห่างด้านบน */
}

.custom-text-field {
  margin-bottom: 1rem;
  max-width: 450px;
  width: 100%;
  background-color: #f9f9f9;
  border-radius: 8px;
}

.custom-text-field .v-input__control {
  border: 1px solid #4c8479;
}

.gradient-btn {
  background: linear-gradient(90deg, #2273cf, #4c8479) !important;
  color: white !important;
  border: none;
  border-radius: 8px;
}

.gradient-btn:hover {
  background: linear-gradient(90deg, #4c8479, #2273cf) !important;
  opacity: 0.9;
}

.create-account-link {
  text-decoration: none;
  color: #4c8479;
  font-weight: bold;
}

.center-text {
  text-align: center;
  color: white;
  text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
}

</style>