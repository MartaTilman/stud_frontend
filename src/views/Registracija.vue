<template>
  <div>
    <h2>Registracija</h2>
    <form @submit.prevent="register">
      <div>
        <label for="Ime">Ime:</label>
        <input type="text" id="ime" v-model="userData.username" required />
      </div>
      <div>
        <label for="Prezime">Prezime:</label>
        <input type="text" id="prezime" v-model="userData.username" required />
      </div>
      <div>
        <label for="email">Email adresa:</label>
        <input type="email" id="email" v-model="userData.email" required />
      </div>
      <div>
        <label for="password">Lozinka:</label>
        <input
          type="password"
          id="password"
          v-model="userData.password"
          required
        />
      </div>
      <button type="submit">Registriraj se</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userData: {
        username: "",
        email: "",
        password: "",
      },
    };
  },
  methods: {
    async register() {
      try {
        const response = await fetch("http://localhost:8080/src/registracija", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(this.userData),
        });
        const data = await response.json();
        console.log(data);
      } catch (error) {
        console.error("Greška prilikom registracije:", error);
      }
    },
  },
};
</script>

<style>
/* Stilovi po želji */
</style>