<script setup>
import { ref } from "vue";
const tab = ref("list");
import MedicamentList from "./components/MedicamentList.vue";
import AddMedicament from "./components/AddMedicament.vue";
</script>

<template>
  <div class="center-content">
    <div class="app">
      <header class="app-bar">
        <h1>Gestion de la Pharmacie</h1>
      </header>

      <div class="container">
        <div class="tabs">
          <button @click="tab = 'list'" :class="{ active: tab === 'list' }">📋 Liste des Médicaments</button>
          <button @click="tab = 'add'" :class="{ active: tab === 'add' }">➕ Ajouter Médicament</button>
        </div>

        <div class="window">
          <div v-if="tab === 'list'">
            <MedicamentList />
          </div>
          <div v-if="tab === 'add'">
            <AddMedicament />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.center-content {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  width: 100vw;
  text-align: center;
  background-image: url('/bg-pharmacie.jpg');
  background-repeat: repeat-y;
  background-size: cover;
}
.app
{
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  color: #2C3E50;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.app-bar {
  background-color: rgba(21, 101, 192, 0.05);
  backdrop-filter: blur(10px);
  color: #2C3E50;
  padding: 0.01rem;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 100;
}

.container {
  margin-top: 5rem;
  width: 100%;
  background-color: rgba(21, 101, 192, 0.05);
  backdrop-filter: blur(10px);
  color: white;
  padding: 0.01rem;
  flex: 1;
  display: flex;
  flex-direction: column;
}

.tabs {
  display: flex;
  justify-content: center;
  margin-bottom: 2rem;
}

.tabs button {
  background: none;
  border: none;
  padding: 1rem;
  cursor: pointer;
  font-size: 1rem;
}

.tabs button.active {
  border-bottom: 2px solid #1565c0;
}

.window {
  text-align: left;
}
</style>