<template>
  <div class="login-container">
    <div class="login-card">
      <div class="image-container">
        <v-img
          class="login-logo"
          max-width="120"
          src="/public/assets/images/unnamed.jpg"
        ></v-img>
      </div>
      <div class="login-header">
        <h2>ยินดีต้อนรับสู่เว็บไซต์</h2>
      </div>

      <v-text-field
        v-model="email"
        label="Email Address"
        class="login-input mb-4"
        :error="emailError"
        :error-messages="emailErrorMessages"
      ></v-text-field>

      <v-text-field
        v-model="passwd"
        label="Password"
        :type="visible ? 'text' : 'password'"
        class="login-input mb-4"
        append-icon="mdi-eye"
        @click:append="visible = !visible"
        :error="passwdError"
        :error-messages="passwdErrorMessages"
      ></v-text-field>

      <v-btn class="login-btn" @click="doLogin">
        Log In
      </v-btn>

      <div class="register-prompt">
        <p>ยังไม่มีบัญชีใช่หรือไม่?</p>
        <a @click="goToRegister">สร้างบัญชีใหม่</a>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
definePageMeta({
  layout: 'minimal'
});
export default {
  data: () => ({
    visible: false,
    email: '',
    passwd: '',
    emailError: false,
    emailErrorMessages: '',
    passwdError: false,
    passwdErrorMessages: ''
  }),
  methods: {
    async doLogin() {
      this.emailError = !this.email;
      this.emailErrorMessages = this.email ? '' : 'Email is required';
      
      this.passwdError = !this.passwd;
      this.passwdErrorMessages = this.passwd ? '' : 'Password is required';

      if (this.emailError || this.passwdError) return;

      let forms = {
        email: this.email,
        passwd: this.passwd
      }

      try {
        const response = await axios.post('http://localhost:7000/login', forms)
        const data = response.data

        if (data.status === 'success') {
          window.sessionStorage.setItem("token", JSON.stringify(data.token));
          this.$router.replace('/data_table')
        } else {
          this.$router.replace('/login')
        }
      } catch (error) {
        console.error("Login error:", error);
      }
    },
    goToRegister() {
      this.$router.push('/reg');
    },
  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600&display=swap');

.login-container {
  background: radial-gradient(circle at center, #000428, #004e92);
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.login-card {
  background: rgba(255, 255, 255, 0.95);
  padding: 60px;
  border-radius: 30px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.3);
  max-width: 420px;
  text-align: center;
  transition: all 0.4s ease;
}

.login-card:hover {
  transform: translateY(-12px);
}

.image-container {
  margin-bottom: 25px;
}

.login-logo {
  width: 110px;
  height: 110px;
  border-radius: 50%;
  border: 2px solid #ddd;
}

.login-header h2 {
  font-family: 'Montserrat', sans-serif;
  font-size: 2.2rem;
  font-weight: 600;
  margin-bottom: 25px;
  color: #333;
}

.login-input {
  margin-bottom: 22px;
  --v-theme-background: #eef3f8;
}

.login-btn {
  width: 100%;
  background: linear-gradient(45deg, #ff6f91, #ff9671);
  color: white;
  font-size: 1.2rem;
  font-family: 'Montserrat', sans-serif;
  font-weight: 600;
  padding: 12px;
  border-radius: 50px;
  transition: background 0.4s ease;
}

.login-btn:hover {
  background: linear-gradient(45deg, #ff9671, #ff6f91);
}

.register-prompt {
  margin-top: 35px;
  font-size: 1.1rem;
  color: #555;
}

.register-prompt a {
  color: #ff9671;
  text-decoration: none;
  font-weight: 600;
}

.register-prompt a:hover {
  text-decoration: underline;
}
</style>
