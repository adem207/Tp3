<template>
  <Page>
    <ActionBar title="Liste des Pages" />
    <StackLayout>
      <Label text="Entrez le titre du journal" class="title" />

      <!-- Champ de saisie pour le titre du journal -->
      <TextField v-model="titreJournal" hint="Titre du journal" class="input" />

      <!-- Bouton pour rechercher les pages -->
      <Button text="Rechercher" @tap="fetchPages" class="search-button" />

      <Label text="Liste des Pages" class="title" />

      <!-- Affichage des pages -->
      <ListView v-if="pages.length > 0" for="page in pages">
        <v-template>
          <StackLayout class="list-item">
            <Label text="Titre:" class="label-title" />
            <Label :text="page.titre" class="item-title" />
            <Label text="Contenu:" class="label-content" />
            <Label :text="page.contenu" class="item-content" />
          </StackLayout>
        </v-template>
      </ListView>

      <!-- Message s'il n'y a aucune page -->
      <Label v-else text="Aucune page disponible" />

      <Label v-if="erreur" :text="erreur" class="error-message" />

      <!-- Bouton pour retourner à l'accueil -->
      <Button text="Retour à l'accueil" @tap="goToHome" class="home-button" />
    </StackLayout>
  </Page>
</template>

<script>
import { getString } from '@nativescript/core/application-settings';
import Accueil from './Accueil.vue'; // Importer la page d'accueil

export default {
  data() {
    return {
      pages: [], // Liste des pages
      erreur: null, // Message d'erreur s'il y a un problème
      titreJournal: '', // Titre du journal saisi par l'utilisateur
    };
  },
  methods: {
    // Méthode pour récupérer les pages via l'API
    async fetchPages() {
      try {
        const token = getString('token');
        console.log('Token utilisé:', token);

        const response = await fetch(`http://10.0.2.2:3000/pages?title=${encodeURIComponent(this.titreJournal)}`, {
          method: 'GET',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${token}`,
          },
        });

        if (!response.ok) {
          throw new Error('Erreur lors de la récupération des pages');
        }

        const data = await response.json();
        console.log('Pages récupérées:', data);
        if (Array.isArray(data)) {
          this.pages = data; // Mettre à jour les pages uniquement si c'est un tableau
        } else {
          console.error('Les données récupérées ne sont pas un tableau');
          this.pages = []; // Réinitialiser les pages si les données ne sont pas conformes
        }
      } catch (error) {
        this.erreur = error.message;
        console.error('Erreur:', error);
      }
    },
    // Méthode pour retourner à l'accueil
    goToHome() {
      this.$navigateTo(Accueil); // Naviguer vers la page d'accueil
    },
  },
};
</script>

<style scoped>
.title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.input {
  margin-bottom: 10px;
}

.list-item {
  padding: 10px;
  border-bottom-width: 1px;
  border-color: #ccc;
}

.label-title, .label-content {
  font-weight: bold;
}

.item-title, .item-content {
  color: #666;
  margin-bottom: 5px; /* Ajout d'espacement entre les lignes */
}

.error-message {
  color: red;
}

.home-button {
  margin-top: 20px;
  background-color: #3b5998;
  color: white;
  padding: 10px;
  border-radius: 5px;
}

.search-button {
  background-color: #007bff;
  color: white;
  padding: 10px;
  border-radius: 5px;
}
</style>
