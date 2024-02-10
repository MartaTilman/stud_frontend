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
    <div class="contener">
      <button @click="saveNote" class="spremi">Spremi</button>
      <button class="uredi">Uredi</button>
      <button class="obrisi">Obriši</button>
      <div class="notes">
        <div class="naslov">
          <input
            type="text"
            class="title"
            v-model="titleInput"
            placeholder="✨Naslov✨"
            required
          />
        </div>
        <br />
        <div>
          <textarea
            id="myInput"
            class="content"
            type="text"
            v-model="contentInput"
            @keydown.enter.prevent="insertNewLine"
            placeholder="Sadržaj ✍🏻"
            required
          ></textarea>
        </div>
      </div>
    </div>
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
import axios from "axios";

export default {
  data() {
    return {
      titleInput: "",
      contentInput: "",
      currentDate: "",
      ime: "",
      prezime: "",
      isPrijavljen: false,
      userDetails: null,
      notes: [],
    };
  },
  created() {
    // Fetch user details when the component is created
    this.fetchUserDetails();
  },
  methods: {
    async getNotes() {
      try {
        const userId = this.fetchUserDetails.id; // Zamijenite s ID-em prijavljenog korisnika
        const response = await axios.get(
          `http://localhost:5000/notes/${userId}`
        );

        // Ažuriramo polje 'notes' s podacima koje dobijemo iz backenda
        this.notes = response.data;
      } catch (error) {
        console.error("Error fetching notes:", error);
      }
    },
    updateDate() {
      this.currentDate = new Date().toLocaleDateString();
    },
    async fetchUserDetails() {
      try {
        // Make a request to the backend to fetch user details
        const token = localStorage.getItem("token"); // Assuming token is stored in local storage
        const response = await axios.get("http://localhost:5000/userDetails", {
          headers: {
            token: token,
          },
        });

        // Set userDetails data in the component
        this.userDetails = response.data.user;
        console.log(this.userDetails);
      } catch (error) {
        console.error("Error fetching user details:", error);
      }
    },
    async saveNote() {
      try {
        console.log(this.userDetails.id);
        // Assuming you have access to the logged-in user's ID
        const userId = this.userDetails.id; // Replace with the method to get the user ID

        const response = await axios.post("http://localhost:5000/notes", {
          title: this.titleInput,
          content: this.contentInput,
          usernotes: userId,
        });

        console.log("Note saved:", response.data);

        // Optionally, you can reset the input fields after saving
        this.titleInput = "";
        this.contentInput = "";
      } catch (error) {
        console.error("Error saving note:", error);
      }
    },
  },
  mounted() {
    this.getNotes();
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
.spremi,
.obrisi,
.uredi {
  position: absolute;
  z-index: 3;
}
.spremi {
  right: 600px;
}
.uredi {
  right: 330px;
}
.obrisi {
  right: 70px;
}
.contener {
  position: relative;
  padding-top: 10px;
}
.notes {
  position: absolute;
  z-index: 3;
  height: 500px;
  width: 700px;
  top: 50px; /* Adjust as needed to move the notes down */
  right: 0;
  border-radius: 10px;
  border: none;
  background: url("@/assets/notes.png") no-repeat;
}
.title {
  border: none;
  outline: none;
  background: none;
  padding-left: 100px;
  padding-top: 40px;
}
.content {
  width: auto;
  height: auto;
  border: none;
  outline: none;
  background: none;
  padding-top: 30px;
  padding-left: 60px;
}

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
  display: grid;
  grid-template-columns: auto auto;
  gap: 10px;
}
.div-kalendar {
  position: relative;
  display: block;
  justify-content: center;
  align-items: center;
  z-index: 3;
  background-color: #008b8b;
  text-align: center;
  width: 200px;
  margin-left: 33%;

  padding: 20px;
  border-radius: 8px;
  margin-top: 25%;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  font-weight: bold;
  color: antiquewhite;
  padding-top: 35px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
}

.slika-nav {
  height: 70px;
  width: auto;
}
.div-chat {
  position: relative;
  display: block;
  justify-content: center;
  align-items: center;
  z-index: 3;
  background-color: #008b8b;
  text-align: center;
  width: 200px;
  margin-left: 33%;

  padding: 20px;
  border-radius: 8px;
  margin-top: 25%;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  font-weight: bold;
  color: antiquewhite;
  padding-bottom: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
}
.slika {
  position: relative;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2;
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
</style>
