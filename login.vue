<template>
<v-app>
    <v-app-bar app color="transparent" elevation="0">
        <v-app-bar-nav-icon>
            <v-icon color="white">mdi-dots-horizontal</v-icon>
        </v-app-bar-nav-icon>
    </v-app-bar>

    <v-main class="login-background d-flex flex-column">
        <v-container fluid fill-height class="pa-0 flex-grow-1">
            <v-row no-gutters class="fill-height flex-column justify-center align-center">
                <v-col class="d-flex flex-column align-center">
                    <div class="circular-image mb-4">
                        <v-img src="https://via.placeholder.com/240" alt="Student Cartoon" width="100%" height="100%" cover></v-img>
                    </div>
                    <div class="text-wrapper mb-6">
                        <div class="student-text font-weight-bold">Student</div>
                        <div class="check-app-text">CHECK APP</div>
                    </div>
                    <v-text-field v-model="email" label="Email or Phone" variant="outlined" clearable class="custom-text-field" outlined color="primary">
                        <template v-slot:prepend-inner>
                            <v-icon color="primary">mdi-account</v-icon>
                        </template>
                    </v-text-field>
                    <v-text-field v-model="password" label="Password" type="password" variant="outlined" clearable class="custom-text-field" outlined color="primary">
                        <template v-slot:prepend-inner>
                            <v-icon color="primary">mdi-lock</v-icon>
                        </template>
                    </v-text-field>
                </v-col>
            </v-row>
        </v-container>
        <v-card class="white-card custom-rounded-top" elevation="2" width="100%">
            <v-card-text>
                <div class="text-center mb-4">
                    <router-link to="/forgot_pass" class="forgot-password-link">
                        Forgot Password?
                    </router-link>
                </div>
                <v-btn color="primary" @click="Login" class="login-btn" block>
                    <span class="white--text">Login</span>
                </v-btn>
                <div class="text-center my-4">or</div>
                <v-btn outlined color="primary" class="create-account-btn" block>
                    <router-link to="/account2" class="create-account-link" @click="register">Create an account</router-link>
                </v-btn>
                <div class="d-flex justify-center mt-4">
                    <v-avatar color="#1877F2" size="large" class="mx-2 clickable-avatar">
                        <v-icon>mdi-facebook</v-icon>
                    </v-avatar>
                    <v-avatar color="#DB4437" size="large" class="mx-2 clickable-avatar">
                        <v-icon>mdi-google</v-icon>
                    </v-avatar>
                </div>
            </v-card-text>
        </v-card>
    </v-main>
</v-app>
</template>

  
<script>
import axios from "axios";
import { useRouter } from 'vue-router';
const router = useRouter();


function register() {
    router.push('/account2');
}

export default {
    data() {
        return {
            email: "",
            password: "",
        };
    },
    methods: {
        async Login() {
            console.log("Username => ", this.email);
            const forms = {
            username: this.email,
            password: this.password,
        };

        

            try {
                const response = await axios.post("http://localhost:7000/login", forms);
                if (response.data.token) {
                    localStorage.setItem("token", response.data.token);
                    localStorage.setItem("student_id", response.data.student_id);
                    localStorage.setItem('username', this.email);
                    localStorage.setItem("picture", response.data.picture); 
                    this.$router.push("/student_profile");
                } else {
                    alert("Invalid username or password");
                }
            } catch (error) {
                console.error("Login failed: ", error);
                alert("Login failed. Please try again.");
            }
        },
    },
};
</script>

  
<style scoped>
.login-background {
    background: linear-gradient(90deg, #0077ff);
    min-height: 100vh;
    padding: 2rem 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.circular-image {
    width: 240px;
    height: 240px;
    border-radius: 50%;
    overflow: hidden;
    box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.2);
    background-color: white;
}

.text-wrapper {
    text-align: center;
}

.student-text {
    font-size: 2.5rem;
    font-weight: bold;
    color: #edb232;
}

.check-app-text {
    font-size: 1.2rem;
    color: white;
    margin-top: 0.5rem;
}

.custom-text-field {
    margin-bottom: 1rem;
    max-width: 400px;
    width: 100%;
}

.custom-text-field .v-input__control {
    background-color: #f5f5f5;
    /* สีพื้นหลังช่องกรอก */
    color: #333;
    /* สีข้อความ */
    border: 1px solid #4c8479;
    /* สีขอบ */
    border-radius: 8px;
}

.custom-text-field .v-label {
    color: #4c8479;
    /* สี label */
}

.custom-text-field .v-input__control:focus-within {
    border-color: #29b79f;
    /* ขอบเมื่อ focus */
    background-color: #e0f7f3;
    /* พื้นหลังเมื่อ focus */
}

.custom-text-field input {
    color: #333;
    /* สีข้อความที่กรอก */
}

.white-card {
    background-color: #fff;
}

.custom-rounded-top {
    border-top-left-radius: 24px;
    border-top-right-radius: 24px;
    padding: 2rem;
}

.forgot-password-link {
    color: #4c8479;
    text-decoration: none;
    font-weight: 500;
}

.create-account-link {
    text-decoration: none;
    color: #ffffff;
    border-radius: 8px;
    font-size: 1.1rem;
    font-weight: bold;
}

.login-btn,
.create-account-btn {
    border-radius: 8px;
    font-size: 1.1rem;
    font-weight: bold;
}

.clickable-avatar {
    cursor: pointer;
    background: linear-gradient(45deg, #6a11cb, #2575fc);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.clickable-avatar:hover {
    transform: scale(1.1);
    box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.3);
}
</style>
