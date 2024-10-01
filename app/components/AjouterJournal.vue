<template>
  <Page>
    <ActionBar title="Ajouter un Journal" />
    <StackLayout>
      <TextField v-model="titre" hint="Titre du Journal" />
      <TextView v-model="contenu" hint="Contenu du Journal" multiline="true" />
      <Button text="Créer Journal" @tap="createJournal" />
      <Button text="Annuler" @tap="cancel" />
      <!-- Afficher dynamiquement le message d'erreur ou de succès -->
      <Label v-if="message" :text="message" class="message" />
      <Label v-if="error" :text="error" class="error" />
    </StackLayout>
  </Page>
</template>

<script>
import axios from "axios/dist/axios";
import Accueil from './Accueil.vue'; // Importer la page d'accueil
import * as ApplicationSettings from '@nativescript/core/application-settings'; // Importer ApplicationSettings

export default {
  data() {
    return {
      titre: '',
      contenu: '',
      message: null,
      error: null,
    };
  },
  methods: {
    async createJournal() {
      console.log("Tentative de création d'un journal...");
      this.message = null;
      this.error = null;

      // Vérification si les champs sont vides
      if (this.titre.trim() === '' || this.contenu.trim() === '') {
        this.error = "Veuillez remplir tous les champs.";
        return;
      }

      try {
        const token = ApplicationSettings.getString('token'); // Récupérer le token depuis ApplicationSettings
        console.log("Token récupéré :", token);

        if (!token) {
          this.error = "Token manquant. Veuillez vous connecter.";
          return;
        }

        const response = await axios.post('http://10.0.2.2:3000/journals', {
          titre: this.titre,
          contenu: this.contenu,
        }, {
          headers: {
            Authorization: `Bearer ${token}`, // Ajouter le token dans les en-têtes
          },
        });

        if (response.status === 201) {
          this.message = 'Entrée de journal ajoutée avec succès';
          this.titre = ''; // Réinitialiser le titre
          this.contenu = ''; // Réinitialiser le contenu
          this.$navigateTo(Accueil); // Naviguer vers la page d'accueil
        }
      } catch (error) {
        console.error("Erreur:", error);
        if (error.response) {
          this.error = error.response.data.message || 'Une erreur est survenue lors de l\'ajout de l\'entrée de journal. Veuillez réessayer.';
        } else {
          this.error = 'Erreur de connexion. Veuillez vérifier votre réseau.';
        }
      }
    },
    cancel() {
      this.$navigateTo(Accueil);
    },
  },
};
</script>

<style scoped>
.error {
  color: red;
  margin
