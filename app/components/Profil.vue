<template>
  <Page>
    <ActionBar title="Profil" />
    <StackLayout>
      <StackLayout orientation="horizontal" verticalAlignment="center">
        <Label text="Nom de l'utilisateur:" class="title" />
        <Label v-if="!isEditing" :text="nomUtilisateur" class="nom-utilisateur" />
        <TextField
          v-if="isEditing"
          v-model="modifiedNom"
          class="input-nom"
          hint="Modifier le nom"
        />
      </StackLayout>
      <Button v-if="!isEditing" text="Modifier le nom" @tap="prepareEdit" />
      <Button v-if="isEditing" text="Enregistrer" @tap="saveChanges" />
      <Button text="Supprimer le compte" @tap="supprimerCompte" />
    </StackLayout>
  </Page>
</template>

<script>
import { ApplicationSettings } from '@nativescript/core';

export default {
  data() {
    return {
      nomUtilisateur: '',
      modifiedNom: '',
      isEditing: false,
    };
  },
  methods: {
    async recupererUtilisateur() {
      try {
        const token = ApplicationSettings.getString('token');
        const response = await fetch('http://10.0.2.2:3000/user', {
          method: 'GET',
          headers: {
            'Authorization': `Bearer ${token}`,
            'Content-Type': 'application/json',
          },
        });

        if (response.ok) {
          const data = await response.json();
          this.nomUtilisateur = data.nom;
          this.modifiedNom = data.nom;
        } else {
          const errorData = await response.json();
          console.error('Erreur lors de la récupération des informations de l\'utilisateur:', errorData);
        }
      } catch (error) {
        console.error('Erreur de connexion:', error);
      }
    },
    prepareEdit() {
      this.isEditing = true;
    },
    async saveChanges() {
      if (!this.modifiedNom) {
        console.error('Le nom ne peut pas être vide');
        return;
      }

      try {
        const token = ApplicationSettings.getString('token');
        const response = await fetch('http://10.0.2.2:3000/user', {
          method: 'PUT',
          headers: {
            'Authorization': `Bearer ${token}`,
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            nom: this.modifiedNom,
          }),
        });

        if (response.ok) {
          const data = await response.json();
          this.nomUtilisateur = this.modifiedNom;
          this.isEditing = false;
        } else {
          const errorData = await response.json();
          console.error('Erreur lors de la sauvegarde des modifications:', errorData);
        }
      } catch (error) {
        console.error('Erreur de connexion:', error);
      }
    },
    async supprimerCompte() {
      try {
        const token = ApplicationSettings.getString('token');
        const response = await fetch('http://10.0.2.2:3000/user', { // Pas d'ID dans l'URL
          method: 'DELETE',
          headers: {
            'Authorization': `Bearer ${token}`,
          },
        });

        if (response.ok) {
          console.log('Compte supprimé avec succès');
          // Optionnel: Redirection ou réinitialisation après suppression
         // ApplicationSettings.clear(); // Supprimer les données locales
          // Rediriger vers une autre page ou rafraîchir l'interface
        } else {
          const errorData = await response.json();
          console.error('Erreur lors de la suppression du compte:', errorData);
        }
      } catch (error) {
        console.error('Erreur de connexion:', error);
      }
    },
  },
  mounted() {
    this.recupererUtilisateur();
  },
};
</script>

<style scoped>
.title {
  font-size: 16px;
  font-weight: bold;
  margin-right: 10px;
}

.nom-utilisateur {
  font-size: 16px;
  font-weight: normal;
}

.input-nom {
  margin: 10px;
  border-radius: 5px;
  padding: 5px;
}

Button {
  margin-top: 15px;
}
</style>
