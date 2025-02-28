<template>
  <div class="add-button">
    <button @click="openModal">Ajouter Médicament</button>

    <!-- Modal -->
    <div v-if="isModalOpen" class="modal">
      <div class="modal-content">
        <span class="close" @click="closeModal">&times;</span>
        <h2 class="title">Ajouter un nouveau médicament</h2>
        <form @submit.prevent="addMedicament">
          <div class="form-group">
            <label for="denomination">Nom:</label>
            <input v-model="newMedicament.denomination" type="text" id="denomination" required>
          </div>
          <div class="form-group">
            <label for="formepharmaceutique">Forme:</label>
            <input v-model="newMedicament.formepharmaceutique" type="text" id="formepharmaceutique" required>
          </div>
          <div class="form-group">
            <label for="qte">Quantité:</label>
            <input v-model="newMedicament.qte" type="number" id="qte" required>
          </div>
          <div class="form-group">
            <label for="photo">Photo:</label>
            <input @change="handleFileUpload" type="file" id="photo">
          </div>
          <button type="submit" class="submit-button">Ajouter</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

// Déclaration des variables réactives
const isModalOpen = ref(false);
const newMedicament = ref({
  denomination: "",
  formepharmaceutique: "",
  qte: 1,
  photo: "",
});
const apiUrl = "https://apipharmacie.pecatte.fr/api/20/medicaments";

// Fonction pour gérer le téléchargement de fichier
const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = () => {
    newMedicament.value.photo = reader.result;
  };
  reader.readAsDataURL(file);
};

// Fonction pour ajouter un médicament
const addMedicament = async () => {
  try {
    const response = await fetch(apiUrl, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(newMedicament.value),
    });
    const result = await response.json();
    if (result.status === 1) {
      alert("Médicament ajouté !");
      newMedicament.value = {
        denomination: "",
        formepharmaceutique: "",
        qte: 1,
        photo: "",
      };
      closeModal();
    } else {
      alert(result.message);
    }
  } catch (error) {
    console.error("Erreur lors de l'ajout du médicament", error);
  }
};

// Fonction pour ouvrir la modal
const openModal = () => {
  isModalOpen.value = true;
};

// Fonction pour fermer la modal
const closeModal = () => {
  isModalOpen.value = false;
  newMedicament.value = {
    denomination: "",
    formepharmaceutique: "",
    qte: 1,
    photo: "",
  };
};
</script>

<style scoped>
.add-button {
  margin-top: 20px;
  text-align: center;
}

.add-button button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 2000; /* Ensure the modal is above the app-bar */
}
.title {
  color: #2C3E50;
}
.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  width: 80%;
  max-width: 500px;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  font-size: 24px;
  cursor: pointer;
  border: none;
  background: none;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #2C3E50; /* Set label color to #2C3E50 */
}

.form-group input {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 5px;
  color: #2C3E50; /* Set input text color to #2C3E50 */
}

.submit-button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  background-color: #1565c0;
  color: white;
  border: none;
  border-radius: 5px;
  margin-top: 10px;
}
</style>