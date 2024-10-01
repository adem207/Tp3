<template>
  <Page>
    <ActionBar title="Ajouter une Page" />
    <StackLayout>
      <TextField v-model="titre" hint="Titre de la Page" />
      <TextView v-model="contenu" hint="Contenu de la Page" multiline="true" />
      <TextField v-model="id_journal" hint="ID du Journal" keyboardType="number" />

      <Button text="Créer Page" @tap="createPage" />
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
import * as appSettings from '@nativescript/core/application-settings'; // Pour gérer le token avec ApplicationSettings

export default {
  data() {
    return {
      id_journal: '',
      titre: '',
      contenu: '',
      message: null,
      error: null,
    };
  },
  methods: {
    async createPage() {
      console.log("Tentative de création d'une page..."); // Log initial
      this.message = null; // Réinitialiser le message de succès
      this.error = null; // Réinitialiser l'erreur

      // Vérification si les champs sont vides
      if (this.titre.trim() === '' || this.contenu.trim() === '' || this.id_journal.trim() === '') {
        this.error = "Veuillez remplir tous les champs.";
        console.log(this.error); // Log de l'erreur
        return; // Arrêter l'exécution si les champs ne sont pas remplis
      }

      console.log("ID Journal:", this.id_journal); // Log de l'ID journal
      console.log("Titre:", this.titre); // Log du titre
      console.log("Contenu:", this.contenu); // Log du contenu

      try {
        const token = appSettings.getString('token'); // Récupérer le token depuis ApplicationSettings
        console.log("Token récupéré:", token); // Log du token

        if (!token) {
          this.error = "Token manquant. Veuillez vous connecter.";
          return;
        }

        console.log("Données de la requête:", {
          id_journal: this.id_journal,
          titre: this.titre,
          contenu: this.contenu,
        });

        const response = await axios.post('http://10.0.2.2:3000/page', {
          id_journal: this.id_journal,
          titre: this.titre,
          contenu: this.contenu,
        }, {
          headers: {
            Authorization: `Bearer ${token}`, // Ajouter le token dans les en-têtes
          },
        });

        // Vérifier si la création de la page est réussie
        console.log("Réponse de l'API:", response); // Log de la réponse
        if (response.status === 201) {
          this.message = 'Page ajoutée avec succès';
          this.id_journal = ''; // Réinitialiser l'ID du journal
          this.titre = ''; // Réinitialiser le titre
          this.contenu = ''; // Réinitialiser le contenu
          this.$navigateTo(Accueil); // Naviguer vers la page d'accueil
        }
      } catch (error) {
        console.error("Erreur:", error); // Log de l'erreur complète
        if (error.response) {
          console.log("Erreur du serveur:", error.response.data); // Log de la réponse d'erreur
          this.error = error.response.data.message || 'Une erreur est survenue lors de l\'ajout de la page. Veuillez réessayer.';
        } else {
          this.error = 'Erreur de connexion. Veuillez vérifier votre réseau.';
        }
      }
    },
    cancel() {
      this.$navigateTo(Accueil); // Naviguer vers la page d'accueil
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
.message {
  color: green;
  margin-top: 10px;
  font-weight: bold;
}
</style>
