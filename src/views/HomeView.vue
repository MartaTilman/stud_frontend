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
        <li>
          <a href="#" v-if="!isPrijavljen" @click="goToRegister">REGISTER</a>
        </li>
        <li><a href="#" v-if="!isPrijavljen" @click="goToLogin">LOGIN</a></li>
        <img
          @click="logout"
          v-if="isPrijavljen"
          src="@/assets/odjava.png"
          alt="odjava"
          class="odjava"
        />
        <router-link to="/">
          <img
            v-if="isPrijavljen"
            src="@/assets/user.png"
            alt="user"
            class="user"
          />
        </router-link>
        <p v-if="isPrijavljen" class="imeiprezime">{{ ime }} {{ prezime }}</p>
      </ul>
    </nav>
    <div class="div-tekst">
      <div class="div-kalendar">
        📝 Zapišite svoje misli uz Notes! Nikad nije bilo lakše pratiti svoje
        ideje i ostati organiziran uz našu inovativnu aplikaciju za bilješke.
        Sada možete jednostavno bilježiti ideje, zadatke i inspiraciju, sve na
        jednom mjestu! Organizirajte svoje misli i budite produktivni kao nikada
        do sada!<br /><br />
        <button v-if="isPrijavljen" @click="goToNotes">NOTES🖋️</button>
      </div>
    </div>
    <div></div>

    <footer class="footer">
      <p><b>&copy; 2024 STUD All rights reserved.</b></p>
      <div id="current-date">
        <span class="date-color"
          ><b>{{ currentDate }}</b></span
        >
      </div>
    </footer>
  </div>
</template>

<script>
export default {
  name: "WelcomePage",

  data() {
    return {
      currentDate: "",
      ime: "",
      prezime: "",
      isPrijavljen: false,
    };
  },
  methods: {
    goToLogin() {
      this.$router.push({ name: "login" });
    },
    goToRegister() {
      this.$router.push({ name: "registracija" });
    },
    updateDate() {
      this.currentDate = new Date().toLocaleDateString();
    },
    goToChat() {
      this.$router.push({ name: "chat" });
    },
    goToNotes() {
      this.$router.push({ name: "useri" });
    },
    logout() {
      localStorage.removeItem("token");
      localStorage.removeItem("ime");
      localStorage.removeItem("prezime");
      this.$router.push({ name: "login" });
    },
  },

  mounted() {
    this.updateDate();
    setInterval(this.updateDate, 86400000);
    this.ime = localStorage.getItem("ime") || "";
    this.prezime = localStorage.getItem("prezime") || "";
    const token = localStorage.getItem("token");
    if (token) {
      this.isPrijavljen = true;
    }
  },
};
</script>

<style scoped>
.imeiprezime {
  text-decoration: none;
  color: white;
  font-weight: bold;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  font-size: 20px;
  transition: color 0.3s ease;
  float: right;
}
.div-tekst {
  display: flex; /* Koristi fleksibilni layout model */
  justify-content: center; /* Centriraj horizontalno unutar roditeljskog elementa */
  align-items: center; /* Centriraj vertikalno unutar roditeljskog elementa */
  flex-direction: column; /* Postavi smjer djece na vertikalno */
  text-align: center; /* Centriraj tekst unutar kontejnera */
}
.div-kalendar {
  position: absolute;
  top: 50%; /* Postavi vrh diva na 50% visine roditeljskog elementa */
  left: 50%; /* Postavi lijevi rub diva na 50% širine roditeljskog elementa */
  transform: translate(
    -50%,
    -50%
  ); /* Centriraj div na sredinu koristeći translate */
  z-index: 3;
  background-color: #008b8b;
  text-align: center;
  width: auto;
  padding: 20px;
  border-radius: 8px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  font-weight: bold;
  color: antiquewhite;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
}

.slika-nav {
  height: 70px;
  width: auto;
}

body {
  margin: 0;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
}

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

.background-image {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: url("@/assets/pozadina.png") no-repeat center center;
  background-size: 100%;
  z-index: 0;
}

.content {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  text-align: center;
  z-index: 1;
}

.title-image {
  max-width: 80%;
  height: auto;
  margin-bottom: 70%;
  background-color: transparent;
  display: block;
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

.footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  color: white;
  text-align: center;
  padding-bottom: 15px;
  z-index: 2;
  background-color: rgba(204, 204, 204, 0.3);
  box-shadow: 0 -2px 5px #454545;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
}
.tekst {
  margin-bottom: 10px;
  display: block;
  text-align: center;
}
.odjava {
  width: 55px;
  height: auto;
  cursor: pointer;
}
button {
  background-color: rgba(251, 113, 70, 0.5);
  color: white;
  padding: 0.7rem 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 18px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  transition: background-color 0.3s ease;
  margin-left: 10px;
}
</style>
