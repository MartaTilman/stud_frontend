<template>
  <div class="welcome-container">
    <div class="background-image"></div>
    <div class="overlay"></div>
    <nav class="navbar">
      <div class="nav-social">
        <router-link to="/">
          <img src="@/assets/logo.png" alt="logo" class="slika-nav" />
        </router-link>
        <a href="https://www.facebook.com" target="_blank"
          ><i class="fab fa-facebook-f"></i
        ></a>
        <a href="https://www.instagram.com" target="_blank"
          ><i class="fab fa-instagram"></i
        ></a>
        <a href="https://www.tiktok.com" target="_blank"
          ><i class="fab fa-tiktok"></i
        ></a>
      </div>
      <ul class="nav-menu">
        <li><a href="#" @click="goToRegister">REGISTER</a></li>
      </ul>
    </nav>
    <div class="login">
      <div class="home-icon" @click="goToMainPage">
        <i class="fas fa-home"></i>
      </div>
      <div class="login_">
        <h2>LOGIN</h2>
        <br />
        <p v-if="loginFailed" class="error-message">Login failed.</p>
        <div class="login-form">
          <form @submit.prevent="loginUser">
            <input type="text" v-model="email" placeholder="Email" required />
            <input
              type="password"
              v-model="password"
              placeholder="Password"
              required
            />
            <button type="submit">LOGIN</button>
          </form>
          <br />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "login",
  data() {
    return {
      showPassword: false,
      email: "",
      password: "",
    };
  },
  computed: {
    buttonLabel() {
      return this.showPassword ? "Hide" : "Show";
    },
  },
  methods: {
    toggleShow() {
      this.showPassword = !this.showPassword;
    },
    loginUser() {
      let user = {
        email: this.email,
        password: this.password,
      };

      axios
        .post("http://localhost:5000/login", user)
        .then((res) => {
          if (res.status === 200) {
            // Spremanje tokena u lokalno skladište
            localStorage.setItem("token", res.data.token);
            localStorage.setItem("ime", res.data.ime);
            localStorage.setItem("prezime", res.data.prezime);

            console.log(res.data);

            this.$router.push("/");
          }
        })
        .catch((err) => {
          alert(err.response.data.error);
        });
    },
  },
};
</script>

<style scoped>
.welcome-container,
.background-image {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  height: 100vh;
  z-index: 0;
}

.welcome-container {
  overflow: hidden;
  padding: none;
  margin: none;
}
.overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1;
}
body {
  margin: 0;
  padding: 0;
}
.background-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: -1;
}

.login {
  position: relative;
  height: 100vh;
  background-size: cover;
  background-position: center;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
}

.login_ {
  background-color: rgba(255, 255, 255, 0.8);
  padding: 80px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
  position: relative;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
}

input {
  display: block;
  margin: 1rem auto;
  padding: 0.7rem;
  border: none;
  border-radius: 5px;
  width: 100%;
  max-width: 500px;
  font-size: 16px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
}
.slika-nav {
  height: 70px;
  width: auto;
  cursor: pointer;
}

button {
  background-color: rgba(0, 255, 0, 0.5);
  color: white;
  padding: 0.7rem 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 18px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: rgba(0, 255, 0, 0.4);
}
.home-icon {
  position: absolute;
  top: 20px;
  left: 30px;
  font-size: 30px;
  cursor: pointer;
  color: white;
}
.background-image {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: url("@/assets/pozadina.png") no-repeat center center;
  background-size: 100%;
  filter: blur(4px);
}
.navbar {
  position: relative;
  z-index: 2;
  width: 100%;
  background-color: rgba(204, 204, 204, 0.3);
  box-shadow: 0 2px 4px #454545;
  padding: 10px 0;
  margin: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.nav-menu {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  padding-right: 20px;
}

.nav-menu li {
  margin-left: 20px;
}

.nav-menu a {
  text-decoration: none;
  color: white;
  font-weight: bold;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  font-size: 20px;
  transition: color 0.3s ease;
}

.nav-menu a:hover {
  color: #f5d826;
}

.nav-social {
  padding-left: 20px;
}

.nav-social a {
  color: white;
  margin-right: 10px;
  font-size: 20px;
  transition: color 0.3s ease;
}

.nav-social a:hover {
  color: #f5d826;
}
</style>