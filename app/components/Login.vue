<template>
  <Page>
    <ActionBar title="Login" />
    <StackLayout>
      <TextField v-model="email" hint="Email" keyboardType="email" />
      <TextField v-model="mot_de_passe" hint="Mot de passe" secure="true" />
      <Button text="Login" @tap="login" />
      <!-- Ajouter un bouton pour rediriger vers la page de registrement -->
      <Button text="Create an account" @tap="goToRegister" />
      <!-- Afficher dynamiquement le message d'erreur, s'il y en a -->
      <Label v-if="error" :text="error" class="error" />
    </StackLayout>
  </Page>
</template>

<script>
import axios from "axios/dist/axios";
import Register from './Register.vue'; // Importer la page de registrement
import Accueil from './Accueil.vue'; // Importer la page d'accueil
import * as ApplicationSettings from '@nativescript/core/application-settings'; // Importer ApplicationSettings

export default {
  data() {
    return {
      email: '',
      mot_de_passe: '',
      error: null, // Initialiser l'erreur à null
    };
  },
  methods: {
    async login() {
      // Réinitialiser l'erreur avant chaque tentative
      this.error = null;

      // Vérification si les champs sont vides
      if (this.email.trim() === '' || this.mot_de_passe.trim() === '') {
        this.error = "Veuillez remplir tous les champs.";
        return;
      }

      try {
        const response = await axios.post('http://10.0.2.2:3000/login', {
          email: this.email,
          mot_de_passe: this.mot_de_passe,
        });

        if (response.data.token) {
          console.log("Token reçu :", response.data.token);
          ApplicationSettings.setString('token', response.data.token); // Stocker le token avec ApplicationSettings

          // Naviguer vers la page d'accueil sur succès
          this.$navigateTo(Accueil);
        } else {
          this.error = 'Identifiants invalides';
        }
      } catch (error) {
        console.log(error);
        this.error = 'Une erreur est survenue lors de la connexion. Veuillez réessayer.';
      }
    },
    goToRegister() {
      this.$navigateTo(Register);
    },
  },
};
</script>

<style scoped>
.error {
  color: red;
  margin-top: 10px;
  font-weight: bold;
}
</style>
