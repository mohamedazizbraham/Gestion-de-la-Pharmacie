<template>
    <div class="search-bar">
      <!-- Barre de recherche -->
      <input
        v-model="search"
        type="text"
        placeholder="Rechercher un médicament"
        @input="updateSearch"
      />
    </div>
  </template>
  
  <script setup>
  import { ref, watch } from "vue";
  
  // Déclaration des propriétés
  const props = defineProps({
    modelValue: String,
  });
  
  // Déclaration des événements émis
  const emit = defineEmits(['update:modelValue']);
  
  // Déclaration de la variable réactive pour la recherche
  const search = ref(props.modelValue);
  
  // Surveillance des changements de la propriété modelValue
  watch(() => props.modelValue, (newValue) => {
    search.value = newValue;
  });
  
  // Fonction pour mettre à jour la recherche
  const updateSearch = () => {
    emit('update:modelValue', search.value);
  };
  </script>
  
  <style scoped>
  .search-bar {
    margin-bottom: 20px;
  }
  
  .search-bar input {
    width: 100%;
    padding: 10px;
    font-size: 16px;
  }
  </style>