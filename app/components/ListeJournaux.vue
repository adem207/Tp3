<template>
  <Page>
    <ActionBar title="Liste des Journaux" />
    <StackLayout>
      <!-- Bouton de retour à l'accueil en position absolue en haut -->
      <Button text="Retour à l'accueil" @tap="cancel" class="home-button absolute-button" />

      <Label text="Liste des Journaux" class="title" />

      <!-- Champs de texte pour modifier le titre et le contenu -->
      <StackLayout v-if="editingId !== null">
        <TextField
          v-model="modifiedTitre"
          class="input-title"
          hint="Modifier le titre"
        />
        <TextView
          v-model="modifiedContenu"
          class="input-content"
          hint="Modifier le contenu"
        />
        <Button
          text="Enregistrer"
          @tap="saveChanges"
          class="save-button"
        />
      </StackLayout>

      <GridLayout>
        <ListView v-if="journaux.length > 0" for="journal in journaux" class="journaux-list">
          <v-template>
            <StackLayout class="list-item">
              <Label text="Titre:" class="label-title" />
              <StackLayout orientation="horizontal" class="item-title-container">
                <Label :text="journal.titre" class="item-title" />
                <Button
                  text="Modifier"
                  @tap="prepareEdit(journal)"
                  class="modify-button"
                />
              </StackLayout>
              <Label text="Contenu:" class="label-content" />
              <Label :text="journal.contenu" class="item-content" />
            </StackLayout>
          </v-template>
        </ListView>

        <Label v-else text="Aucun journal disponible" class="no-journal-message" />
        <Label v-if="erreur" :text="erreur" class="error-message" />
      </GridLayout>
    </StackLayout>
  </Page>
</template>

<script>
import { getString } from '@nativescript/core/application-settings';
import Accueil from './Accueil.vue';

export default {
  data() {
    return {
      journaux: [],
      erreur: null,
      editingId: null,
      modifiedTitre: '',
      modifiedContenu: '',
    };
  },
  created() {
    this.fetchJournaux();
  },
  methods: {
    async fetchJournaux() {
      try {
        const token = getString('token');
        const response = await fetch('http://10.0.2.2:3000/journals', {
          method: 'GET',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${token}`,
          },
        });

        if (!response.ok) {
          throw new Error('Erreur lors de la récupération des journaux');
        }

        const data = await response.json();
        this.journaux = data;
      } catch (error) {
        this.erreur = error.message;
        console.error('Erreur:', error);
      }
    },
    prepareEdit(journal) {
      this.editingId = journal.id;
      this.modifiedTitre = journal.titre; // Préremplir le champ titre
      this.modifiedContenu = journal.contenu; // Préremplir le champ contenu
    },
    async saveChanges() {
      try {
        const token = getString('token');
        const response = await fetch(`http://10.0.2.2:3000/journals/${this.editingId}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${token}`,
          },
          body: JSON.stringify({
            titre: this.modifiedTitre,
            contenu: this.modifiedContenu,
          }),
        });

        if (!response.ok) {
          throw new Error('Erreur lors de la sauvegarde des modifications');
        }

        this.editingId = null; // Réinitialiser l'ID de modification
        this.modifiedTitre = ''; // Réinitialiser le champ titre
        this.modifiedContenu = ''; // Réinitialiser le champ contenu
        this.fetchJournaux(); // Rafraîchir la liste des journaux
      } catch (error) {
        this.erreur = error.message;
        console.error('Erreur:', error);
      }
    },
    cancel() {
      this.$navigateTo(Accueil);
    },
  },
};
</script>

<style scoped>
.input-title,
.input-content {
  margin: 10px;
  border-radius: 5px;
  padding: 5px;
}
.save-button {
  margin: 10px;
}
.item-title-container {
  flex-direction: row;
  justify-content: space-between; /* Équilibre l'espace entre le titre et le bouton */
  align-items: center; /* Centre verticalement les éléments */
}
.modify-button {
  margin-left: 10px;
}
.item-title {
  flex-grow: 1; /* Permet au titre d'occuper l'espace disponible */
}
.journaux-list {
  height: 100%; /* Ajout d'une hauteur explicite pour le ListView */
}
.absolute-button {
  position: absolute;
  top: 20px;  /* Positionné en haut */
  right: 20px;  /* Ajustez la position horizontale */
}
</style>
