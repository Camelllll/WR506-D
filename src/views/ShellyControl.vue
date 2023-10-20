<template>
  <div class="container">
    <div class="center-content">
      <p>État de la prise : {{ etatPrise ? 'Allumée' : 'Éteinte' }}</p>
      <p>Température : {{ temperature }} °C</p>
      <button @click="allumerPrise" :disabled="etatPrise" class="custom-button">Allumer</button>
      <button @click="eteindrePrise" :disabled="!etatPrise" class="custom-button-2">Éteindre</button>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const etatPrise = ref(false);
    const temperature = ref('');
    const on = ref(false);

    const controlPrise = async (action) => {
      try {
        const myHeaders = new Headers();
        myHeaders.append('Content-Type', 'application/json');

        const formdata = new FormData();
        formdata.append('channel', '1');
        formdata.append('turn', 'on');
        formdata.append('device_id', '80646F827174');
        formdata.append('auth_key', 'MWRmYzM2dWlkE62C6C4C76F817CE0A3D2902F5B5D4C115E49B28CF8539114D9246505DE5D368D560D06020A92480');

        const response = await fetch('https://shelly-86-eu.shelly.cloud/device/relay/control', formdata, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        });
        const result = await response.text();
        console.log(result);

        etatPrise.value = action === 'on';
      } catch (error) {
        console.error(error);
      }
    };

    const allumerPrise = () => {
      controlPrise('on');
    };

    const eteindrePrise = () => {
      controlPrise('off');
    };

    return {
      etatPrise,
      temperature,
      allumerPrise,
      eteindrePrise,
    };
  },
};
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.center-content {
  text-align: center;
}

img {
  width: 100px;
  height: 100px;
  margin: 0 auto;
}

.custom-button-2 {
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 10px;
  border-radius: 5px;
  cursor: pointer;
  background-color: rgba(255, 0, 0, 0.12);
}

.custom-button {
  background-color: #2196F3;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 10px;
  border-radius: 5px;
  cursor: pointer;
  background-color: rgba(252, 218, 52, 0.12);
}

.custom-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.custom-image {
  max-width: 80%;
  max-height: 800%;
  margin-bottom: 20px;
}
</style>
