<template>
  <Page>
    <ActionBar title="Accueil" />
    <StackLayout>
      <Label text="Accueil" class="title" />
      <Button text="Nouveau Journal" @tap="nouveauJournal" />
      <Button text="Nouvelle Page" @tap="nouvellePage" />
      <Button text="Liste des Journaux" @tap="listeJournaux" />
      <Button text="Liste des Pages" @tap="listePages" />
      <Button text="Profil" @tap="profil" />
    </StackLayout>
  </Page>
</template>

<script>
import AjouterJournal from './AjouterJournal.vue';
import AjouterPage from './AjouterPage.vue';
import ListeJournaux from './ListeJournaux.vue';
import ListePages from './ListePages.vue'; // Ajoutez l'import pour Liste des Pages
import Profil from './Profil.vue'; // Importer le composant Profil

export default {
  methods: {
    nouveauJournal() {
      console.log('Nouveau Journal clicked');
      this.$navigateTo(AjouterJournal);
    },
    nouvellePage() {
      console.log('Nouvelle Page clicked');
      this.$navigateTo(AjouterPage);
    },
    listeJournaux() {
      console.log('Liste des Journaux clicked');
      this.$navigateTo(ListeJournaux);
    },
    listePages() {
      console.log('Liste des Pages clicked');
      this.$navigateTo(ListePages);
    },
    profil() {
      console.log('Profil clicked');
      this.$navigateTo(Profil); // Rediriger vers la page de profil
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
Button {
  margin-bottom: 15px;
}
</style>
