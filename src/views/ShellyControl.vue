<template>
  <div class="container">
    <div class="center-content">
      <img v-if="etatPrise" class="custom-image" src="src/assets/images/pikaok.png" alt="Allumée" />
      <img v-else class="custom-image" src="src/assets/images/pikako.png" alt="Éteinte" />
      <p>État de la prise : {{ etatPrise ? 'Allumée' : 'Éteinte' }}</p>
      <button @click="allumerPrise" :disabled="etatPrise" class="custom-button">Allumer</button>
      <button @click="eteindrePrise" :disabled="!etatPrise" class="custom-button-2">Éteindre</button>
      <p>Température : <span v-if="temperature">{{ temperature }}°C</span></p>
      <p>Adresse IP : <span v-if="adresseIP">{{ adresseIP }}</span></p>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const etatPrise = ref(true);
    const temperature = ref(0); 
    const adresseIP = ref('');
    const puissance = ref(0);

    const fetchData = async () => {
      try {
        const response = await fetch(
          'https://shelly-77-eu.shelly.cloud/device/status?id=4022d88e30e8&auth_key=MWNiMjY5dWlk404459961993DCA83AE44BC6E3A6F58906952E7BECA0A5B69DC375C964915ACBC0EA536A0639CB73'
        );
        const data = await response.json();

        if (data.isok) {
          etatPrise.value = data.data.device_status.relays[0].ison;
          temperature.value = data.data.device_status.temperature;
          adresseIP.value = data.data.device_status.wifi_sta.ip;
          puissance.value = data.data.device_status.meters.timestamp;
        }
      } catch (error) {
        console.error(error);
      }
    };

    onMounted(() => {
      fetchData();
    });

    const controlPrise = async (action) => {
      try {
        const formdata = new URLSearchParams();
        formdata.append('channel', '0');
        formdata.append('turn', 'on');
        formdata.append('device_id', '4022d88e30e8');
        formdata.append('auth_key', 'MWNiMjY5dWlk404459961993DCA83AE44BC6E3A6F58906952E7BECA0A5B69DC375C964915ACBC0EA536A0639CB73');

        const response = await fetch('https://shelly-77-eu.shelly.cloud/device/relay/control', {
          method: 'POST',
          body: formdata,
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
          },
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
      adresseIP,
      puissance,
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

center-content {
  text-align: center;
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

.custom-image{
  width: 200px;
  height: 200px;
}

</style>
