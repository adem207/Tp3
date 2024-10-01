<template>
  <Page>
    <ActionBar title="Register" />
    <StackLayout>
      <TextField v-model="nom" hint="Nom" />
      <TextField v-model="email" hint="Email" keyboardType="email" />
      <TextField v-model="mot_de_passe" hint="Mot de passe" secure="true" />
      <Button text="Register" @tap="register" />
      <Button text="Already have an account? Login" @tap="goToLogin" />
      <Label v-if="error" :text="error" class="error" />
      <Label v-if="success" :text="success" class="success" />
    </StackLayout>
  </Page>
</template>

<script>
import axios from "axios/dist/axios";
import Login from './Login.vue';

export default {
  data() {
    return {
      nom: '',
      email: '',
      mot_de_passe: '',
      error: null,
      success: null,
    };
  },
  methods: {
    async register() {
      this.error = null;
      this.success = null;

      if (!this.nom || !this.email || !this.mot_de_passe) {
        this.error = "Tous les champs doivent être remplis.";
        return;
      }

      try {
        const response = await axios.post('http://10.0.2.2:3000/register', {
          nom: this.nom,
          email: this.email,
          mot_de_passe: this.mot_de_passe,
        });

        if (response.status === 201) {
          this.success = 'Utilisateur enregistré avec succès!';
          console.log(response.data);
        } else {
          this.error = 'Erreur lors de l\'enregistrement';
        }
      } catch (error) {
        console.log(error);
        this.error = error.response?.data?.message || 'Une erreur est survenue lors de l\'enregistrement. Veuillez réessayer.';
      }
    },
    goToLogin() {
      this.$navigateTo(Login);
    }
  },
};
</script>

<style scoped>
.error {
  color: red;
  margin-top: 10px;
  font-weight: bold;
}
.success {
  color: green;
  margin-top: 10px;
  font-weight: bold;
}
</style>
