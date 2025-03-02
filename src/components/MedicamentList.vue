<template>
  <div class="container">
    <!-- Barre de recherche -->
    <SearchBar v-model="search" @update:modelValue="fetchMedicaments" />

    <!-- Tableau des médicaments -->
    <table class="medicament-table">
      <thead>
        <tr>
          <th>Nom</th>
          <th>Forme</th>
          <th>Quantité</th>
          <th>Image</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="medicament in filteredMedicaments" :key="medicament.id">
          <td>
            <span v-if="!medicament.isEditing">{{ medicament.denomination }}</span>
            <input v-else v-model="medicament.denomination" type="text" />
          </td>
          <td>
            <span v-if="!medicament.isEditing">{{ medicament.formepharmaceutique }}</span>
            <input v-else v-model="medicament.formepharmaceutique" type="text" />
          </td>
          <td>
            <button @click="updateQuantity(medicament.id, +1)">+</button>
            {{ medicament.qte }}
            <button @click="updateQuantity(medicament.id, -1)">-</button>
          </td>
          <td>
            <img :src="getImageUrl(medicament.photo)" @error="imageError" />
            <input v-if="medicament.isEditing" type="file" @change="handleFileUpload($event, medicament)" />
          </td>
          <td>
            <button v-if="!medicament.isEditing" @click="modifyMedicament(medicament)">Modifier</button>
            <button v-else @click="saveMedicament(medicament)">Enregistrer</button>
            <button @click="deleteMedicament(medicament.id)">Supprimer</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import SearchBar from "./SearchBar.vue";

// Déclaration des variables réactives
const medicaments = ref([]);
const search = ref("");
const apiUrl = "https://apipharmacie.pecatte.fr/api/8/medicaments";
const defaultImage = "https://via.placeholder.com/80x50?text=No+Image";
const photo = ref(null);
const isPhotoChanged = ref(false);

// Fonction pour récupérer les médicaments
const fetchMedicaments = async () => {
  try {
    const response = await fetch(`${apiUrl}?search=${search.value}`);
    console.log(search.value);
    medicaments.value = await response.json();
    medicaments.value.forEach(medicament => {
      medicament.isEditing = false;
    });
  } catch (error) {
    console.error("Erreur lors du chargement des médicaments", error);
  }
};

// Fonction pour mettre à jour la quantité
const updateQuantity = async (id, amount) => {
  try {
    const medicament = medicaments.value.find(m => m.id === id);
    if (medicament) {
      const updatedMedicament = {
        id: medicament.id,
        denomination: medicament.denomination,
        formepharmaceutique: medicament.formepharmaceutique,
        qte: medicament.qte + amount,
      };

      const response = await fetch(apiUrl, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(updatedMedicament),
      });

      const result = await response.json();
      if (result.status === 1) {
        medicament.qte += amount;
      } else {
        console.error("Erreur lors de la mise à jour de la quantité", result.message);
      }
    }
  } catch (error) {
    console.error("Erreur lors de la mise à jour de la quantité", error);
  }
};

// Fonction pour supprimer un médicament
const deleteMedicament = async (id) => {
  try {
    await fetch(`${apiUrl}/${id}`, { method: "DELETE" });
    fetchMedicaments();
  } catch (error) {
    console.error("Erreur lors de la suppression du médicament", error);
  }
};

// Fonction pour modifier un médicament
const modifyMedicament = (medicament) => {
  medicament.isEditing = true;
  isPhotoChanged.value = false;
};

// Fonction pour enregistrer un médicament
const saveMedicament = async (medicament) => {
  try {
    const updatedMedicament = {
      id: medicament.id,
      denomination: medicament.denomination,
      formepharmaceutique: medicament.formepharmaceutique,
      qte: medicament.qte,
    };

    if (isPhotoChanged.value) {
      updatedMedicament.photo = medicament.photo;
    }

    const response = await fetch(apiUrl, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(updatedMedicament),
    });
    const result = await response.json();
    if (result.status === 1) {
      medicament.isEditing = false;
      medicament.photo = result.photo || medicament.photo;
    } else {
      console.error("Erreur lors de la modification du médicament", result.message);
    }
  } catch (error) {
    console.error("Erreur lors de la modification du médicament", error);
  }
};

// Fonction pour gérer le téléchargement de fichier
const handleFileUpload = (event, medicament) => {
  const file = event.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = () => {
    medicament.photo = reader.result;
    isPhotoChanged.value = true;
  };
  reader.readAsDataURL(file);
};

// Fonction pour obtenir l'URL de l'image
const getImageUrl = (photo) => {
  return photo ? `https://apipharmacie.pecatte.fr/images/${photo}` : defaultImage;
};

// Fonction pour gérer l'erreur d'image
const imageError = (event) => {
  event.target.src = defaultImage;
};

// Filtrer les médicaments
const filteredMedicaments = computed(() =>
  medicaments.value.filter(m =>
    m.denomination.toLowerCase().includes(search.value.toLowerCase())
  )
);

onMounted(fetchMedicaments);
</script>

<style scoped>
.container {
  width: 100%;
  margin: 0 auto;
}

.medicament-table {
  width: 100%;
  border-collapse: collapse;
}

.medicament-table th,
.medicament-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
  color: #2C3E50;
}

.medicament-table th {
  background-color: #f2f2f2;
}

.medicament-table img {
  height: 50px;
  width: 80px;
}

.medicament-table button {
  margin: 0 5px;
  padding: 5px 10px;
  cursor: pointer;
}
</style>