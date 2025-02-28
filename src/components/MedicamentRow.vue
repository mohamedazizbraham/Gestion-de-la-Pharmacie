<template>
    <tr>
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
        <button v-if="!medicament.isEditing" @click="$emit('modify-medicament', medicament)">Modifier</button>
        <button v-else @click="$emit('save-medicament', medicament)">Enregistrer</button>
        <button @click="$emit('delete-medicament', medicament.id)">Supprimer</button>
      </td>
    </tr>
  </template>
  
  <script setup>
  import { ref } from "vue";
  
  // Déclaration des propriétés
  const props = defineProps({
    medicament: Object,
  });
  
  // Déclaration des événements émis
  const emit = defineEmits(['update-quantity', 'modify-medicament', 'save-medicament', 'delete-medicament']);
  
  // Fonction pour obtenir l'URL de l'image
  const getImageUrl = (photo) => {
    return photo ? `https://apipharmacie.pecatte.fr/images/${photo}` : "https://via.placeholder.com/80x50?text=No+Image";
  };
  
  // Fonction pour gérer l'erreur d'image
  const imageError = (event) => {
    event.target.src = "https://via.placeholder.com/80x50?text=No+Image";
  };
  
  // Fonction pour gérer le téléchargement de fichier
  const handleFileUpload = (event, medicament) => {
    const file = event.target.files[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = () => {
      medicament.photo = reader.result; // Convertir le contenu du fichier en Base64
      medicament.isPhotoChanged = true; // Définir le drapeau pour indiquer que la photo a été modifiée
    };
    reader.readAsDataURL(file);
  };

  const updateQuantity = async (id, amount) => {
  try {
    const response = await fetch(`https://apipharmacie.pecatte.fr/api/20/medicaments/${id}/updateQte`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ amount }),
    });
    if (response.ok) {
      emit('update-quantity', id, amount);
    } else {
      console.error("Erreur lors de la mise à jour de la quantité");
    }
  } catch (error) {
    console.error("Erreur lors de la mise à jour de la quantité", error);
  }
};
  </script>
  
  <style scoped>
  img {
    height: 50px;
    width: 80px;
  }
  button {
    margin: 0 5px;
    padding: 5px 10px;
    cursor: pointer;
    background-color: #1565c0;
    color: white;
    border: none;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
    transition: background-color 0.3s ease;
  }
  
  button:hover {
    background-color: #0d47a1;
  }
  
  button:active {
    background-color: #0b3c91;
  }
  
  button:disabled {
    background-color: #9e9e9e;
    cursor: not-allowed;
  }
  </style>