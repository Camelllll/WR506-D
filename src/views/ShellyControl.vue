<template>
    <div>
      <p>État de la prise : {{ etatPrise ? 'Allumée' : 'Éteinte' }}</p>
      <button @click="allumerPrise" :disabled="etatPrise">Allumer</button>
      <button @click="eteindrePrise" :disabled="!etatPrise">Éteindre</button>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  
  const etatPrise = ref(false);
  
  const recupererEtatPrise = async () => {
    try {
      const response = await fetch('http://192.168.1.100/status'); 
      const data = await response.json();
      etatPrise.value = data.ison;
    } catch (error) {
      console.error(error);
    }
  };
  
  const allumerPrise = async () => {
    try {
      await fetch('http://192.168.1.100/relay/0?turnon', {
        method: 'POST',
      });  
      etatPrise.value = true;
    } catch (error) {
      console.error(error);
    }
  };
  
  const eteindrePrise = async () => {
    try {
      await fetch('http://192.168.1.100/relay/0?turnoff', {
        method: 'POST',
      }); 
      etatPrise.value = false;
    } catch (error) {
      console.error(error);
    }
  };
  
  onMounted(() => {
    recupererEtatPrise();
  });
  </script>
  