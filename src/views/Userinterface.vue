
  <template>
  <div class="welcome-container">
    <div class="background-image"></div>

    <div class="overlay"></div>
    <!-- pocinje navbar -->
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
    <!-- zavrsava navbar -->
    <div class="prikaz">
      <!-- prikaz notesa-->
      <div class="note-container">
        <div v-for="note in notes" :key="note.id">
          <div class="note">
            <div class="get_naslov">
              <br />
              <span>{{ note.title }}</span>
            </div>

            <div>
              <p>{{ note.content }}</p>
            </div>
            <button class="obrisi" @click="deleteNotes(note._id)">
              Obriši
            </button>
            <button
              class="azuriraj_biljesku"
              v-if="!isUpdateFormVisible"
              @click="showUpdateForm(note)"
            >
              Ažuriraj bilješku
            </button>
          </div>
        </div>
      </div>
      <!-- zavrsava prikaz notesa -->
      <!-- update form -->
      <div class="updateform">
        <form
          v-if="isUpdateFormVisible"
          @submit.prevent="updateNote(selectedNoteId)"
        >
          <div class="updateform1">
            <div class="naslov">
              <input
                type="text"
                class="title2"
                v-model="updateTitle"
                placeholder="✨Naslov✨"
                required
              />
            </div>

            <div class="slovaupdate">
              <textarea
                class="content2"
                type="text"
                v-model="updateContent"
                placeholder="Sadržaj ✍🏻"
                required
              ></textarea>
              <button @click="updateNote(selectedNoteId)" class="azuriraj">
                AŽURIRAJ
              </button>
            </div>
          </div>
        </form>
      </div>
      <!-- zavrsava update form -->
      <!-- spremanje notesa -->
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
        <button @click="saveNote" class="spremi">SPREMI</button>
      </div>
      <!-- zavrsava spremanje notesa -->
    </div>
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
      updateTitle: "",
      updateContent: "",
      selectedNoteId: null,
      isUpdateFormVisible: false,
    };
  },
  created() {
    // Fetch user details when the component is created
    this.fetchUserDetails();
  },
  methods: {
    showUpdateForm(note) {
      this.updateTitle = note.title;
      this.updateContent = note.content;
      this.selectedNoteId = note._id;
      this.isUpdateFormVisible = true;
    },

    // Metoda za ažuriranje bilješke
    async updateNote(noteId) {
      try {
        console.log(noteId);
        const { updateTitle, updateContent } = this;

        // Poziv backend API-ja za ažuriranje bilješke
        await axios.put(`https://startling-tarsier-c29251.netlify.app/notes/${noteId}`, {
          title: updateTitle,
          content: updateContent,
        });

        // Osvježi podatke bilješki
        await this.fetchNotes();

        // Resetiraj formu
        this.updateTitle = "";
        this.updateContent = "";
        this.selectedNoteId = null;
      } catch (error) {
        console.error("Error updating note:", error);
      }
    },

    logout() {
      localStorage.removeItem("token");
      localStorage.removeItem("ime");
      localStorage.removeItem("prezime");
      this.$router.push({ name: "login" });
    },

    updateDate() {
      this.currentDate = new Date().toLocaleDateString();
    },
    async fetchUserDetails() {
      try {
        // Make a request to the backend to fetch user details
        const token = localStorage.getItem("token"); // Assuming token is stored in local storage
        const response = await axios.get("https://startling-tarsier-c29251.netlify.app/userDetails", {
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
    async fetchNotes() {
      try {
        console.log("uzima li" + this.userDetails.id);
        const response = await axios.get(
          "https://startling-tarsier-c29251.netlify.app/notes/" + this.userDetails.id
        );

        this.notes = response.data;
      } catch (error) {
        console.error("Error fetching notes:", error);
      }
    },
    async fetchUserDetailsAndNotes() {
      try {
        await this.fetchUserDetails(); // Fetch user details first
        console.log(this.userDetails.id);
        await this.fetchNotes(); // Then fetch notes for the user
      } catch (error) {
        console.error("Error fetching user details and notes:", error);
      }
    },
    async deleteNotes(noteId) {
      try {
        console.log(noteId);
        await axios.delete(`https://startling-tarsier-c29251.netlify.app/notes/${noteId}`);
        await this.fetchNotes();
      } catch (error) {
        console.error("Failed to delete note:", error);
      }
    },

    async saveNote() {
      try {
        console.log(this.userDetails.id);
        // Assuming you have access to the logged-in user's ID
        const userId = this.userDetails.id; // Replace with the method to get the user ID

        const response = await axios.post("https://startling-tarsier-c29251.netlify.app/notes", {
          title: this.titleInput,
          content: this.contentInput,
          usernotes: userId,
        });
        window.location.reload();
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
    this.fetchUserDetailsAndNotes();
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
.title2,
.content2 {
  border: none;
  outline-color: white;
  background: none;
  color: azure;
}
.slovaupdate {
  color: azure;
}
.azuriraj {
  z-index: 3;
  background-color: rgba(161, 250, 88, 0.5);
  color: white;
  padding: 0.7rem 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 18px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  transition: background-color 0.3s ease;
  margin-left: 45px;
}
.prikaz {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr; /* Tri jednake kolone */
  gap: 20px; /* Razmak između kolona */
}
.azuriraj_biljesku {
  background-color: rgba(251, 113, 70, 0.5);
  color: white;
  padding: 0.7rem 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 18px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  transition: background-color 0.3s ease;
  margin-left: 20px;
}
.updateform1 {
  background-color: rgba(164, 196, 253, 0.5);
  color: white;
  padding: 0.7rem 1rem;
  border: none;
  border-radius: 5px;
  width: 200px;
  font-size: 18px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  transition: background-color 0.3s ease;
  margin-top: 150px;
  position: relative;
  grid-column: 2;
  margin-right: 100px;
}
.note-container {
  margin-top: 30px;
  margin-left: 40px;
  max-height: 500px; /* Postavite maksimalnu visinu kontejnera */
  overflow-y: auto;
  scrollbar-width: none; /* Sakriti standardne scroll trake u Firefoxu */
  -ms-overflow-style: none;
  position: relative; /* Dodajte ovu liniju kako bi se updateform pozicionirao u odnosu na ovaj element */
  padding-top: 10px;
  grid-column: 1;
}
.note-container::-webkit-scrollbar {
  /* Sakriti scroll trake za WebKit preglednike (Chrome, Safari, Opera) */
  display: none;
}

.marks {
  padding-left: 100px;
  padding-top: 50px;
}
.get_naslov {
  text-transform: uppercase;
}
.note {
  background-color: rgba(164, 196, 253, 0.5);
  color: white;
  padding: 0.7rem 1rem;
  border: none;
  border-radius: 5px;
  width: 200px;
  font-size: 18px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  transition: background-color 0.3s ease;
  margin-bottom: 20px;
}
.obrisi {
  background-color: rgba(251, 113, 70, 0.5);
  color: white;
  padding: 0.7rem 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 18px;
  font-family: Cambria, Cochin, Georgia, Times, "Times New Roman", serif;
  transition: background-color 0.3s ease;
  margin-left: 60px;
  margin-bottom: 10px;
}
.spremi {
  position: absolute;
  z-index: 3;
  right: 70px;
  background-color: rgba(161, 250, 88, 0.5);
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
  background-color: #FF8369(0, 255, 0, 0.4);
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
  top: 150px; /* Adjust as needed to move the notes down */
  right: 0;
  border-radius: 10px;
  border: none;
  background: url("@/assets/notes.png") no-repeat;
  grid-column: 3;
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
  z-index: 0;
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
