<template>
  <div class="register-container">
    <div class="header">
      <v-img
        class="logo"
        max-width="100"
        src=""
      ></v-img>
      <h1 class="header-title">Join Us Today!</h1>
    </div>

    <v-card class="register-card" elevation="16" max-width="500" rounded="lg">
      <h2 class="register-title">Create Your Account</h2>
      
      <v-form>
        <v-text-field
          v-model="username"
          label="Username"
          placeholder="Choose a username"
          prepend-inner-icon="mdi-account-circle-outline"
          variant="outlined"
          color="blue"
          hide-details
        ></v-text-field>

        <v-text-field
          v-model="email"
          label="Email"
          placeholder="Your email address"
          prepend-inner-icon="mdi-email-outline"
          variant="outlined"
          color="blue"
          hide-details
        ></v-text-field>

        <v-text-field
          v-model="passwd"
          label="Password"
          placeholder="Set your password"
          :type="visible ? 'text' : 'password'"
          append-inner-icon="mdi-eye"
          prepend-inner-icon="mdi-lock-outline"
          variant="outlined"
          color="blue"
          hide-details
          @click:append-inner="visible = !visible"
        ></v-text-field>

        <v-text-field
          v-model="confirmPasswd"
          label="Confirm Password"
          placeholder="Repeat your password"
          :type="visible ? 'text' : 'password'"
          append-inner-icon="mdi-eye"
          prepend-inner-icon="mdi-lock-outline"
          variant="outlined"
          color="blue"
          hide-details
          @click:append-inner="visible = !visible"
        ></v-text-field>

        <v-btn
          class="register-btn"
          block
          @click="doRegister"
          elevation="2"
        >
          Start Now
        </v-btn>
      </v-form>

      <div class="login-redirect">
        <p>Already a member?</p>
        <a @click="goToLogin">Log In</a>
      </div>
    </v-card>
  </div>
</template>

<script>
import axios from 'axios';
definePageMeta({
  layout: 'minimal'
});
export default {
  data: () => ({
    visible: false,
    username: '',
    email: '',
    passwd: '',
    confirmPasswd: ''
  }),
  methods: {
    async doRegister() {
      if (!this.username || !this.email || !this.passwd || this.passwd !== this.confirmPasswd) {
        alert('Please fill out all fields correctly.');
        return;
      }

      const forms = {
        username: this.username,
        email: this.email,
        password: this.passwd
      };

      try {
        const response = await axios.post('http://localhost:7000/insert', forms);
        if (response.data.ok) {
          this.$router.replace('/login');
        } else {
          console.error('Registration failed');
        }
      } catch (error) {
        console.error('Error registering:', error);
      }
    },
    goToLogin() {
      this.$router.push('/login');
    }
  }
};
</script>

<style scoped>
.register-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(135deg, #a1c4fd, #c2e9fb);
  padding: 40px;
}

.header {
  text-align: center;
  margin-bottom: 20px;
}

.logo {
  width: 100px;
  margin-bottom: 10px;
}

.header-title {
  color: #333;
  font-size: 2.5rem;
  font-weight: 600;
  font-family: 'Roboto', sans-serif;
}

/* Card style with a clean and modern look */
.register-card {
  background: white;
  padding: 40px;
  border-radius: 16px;
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.2);
}

.register-title {
  text-align: center;
  font-size: 28px;
  font-weight: 500;
  margin-bottom: 20px;
  color: #007BFF;
}

/* Text fields with modern outlined style */
.v-text-field {
  background-color: #f8f9fa;
  border-radius: 8px;
  margin-bottom: 20px;
}

/* Button styling */
.register-btn {
  background-color: #007BFF;
  color: white;
  font-weight: 600;
  padding: 12px;
  border-radius: 30px;
  transition: background-color 0.3s ease;
}

.register-btn:hover {
  background-color: #0056b3;
}

.login-redirect {
  text-align: center;
  margin-top: 20px;
}

.login-redirect p {
  color: #555;
}

.login-redirect a {
  color: #007BFF;
  font-weight: 600;
  text-decoration: none;
}

.login-redirect a:hover {
  text-decoration: underline;
}
</style>
