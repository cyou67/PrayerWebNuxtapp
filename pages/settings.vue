<template>
    <div class="form-container">
      <h2>Location Settings</h2>
      <label for="city">City</label>
      <input type="text" v-model="city" id="city" placeholder="Enter your city" />
  
      <label for="country">Country</label>
      <input type="text" v-model="country" id="country" placeholder="Enter your country" />
  
      <p>OR</p>
  
      <button @click="getLocation">Get Location</button>
  
      <h2>Calculation Method</h2>
      <select v-model="calculationMethod">
        <option v-for="(method, index) in calculationMethods" :key="index" :value="method">
          {{ method }}
        </option>
      </select>
  
      <button @click="saveSettings">Save Settings</button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        city: '',
        country: '',
        calculationMethod: '',
        calculationMethods: [
          'Muslim World League',
          'Islamic Society of North America (ISNA)',
          'Egyptian General Authority of Survey',
          'Umm Al-Qura University, Makkah',
          'University of Islamic Sciences, Karachi',
          'Institute of Geophysics, University of Tehran',
          'Shia Ithna-Ansari, Leva Institute, Qum'
        ]
      };
    },
    mounted() {
      this.loadSettings();
    },
    methods: {
      loadSettings() {
        const city = localStorage.getItem('city');
        const country = localStorage.getItem('country');
        const method = localStorage.getItem('calculationMethod');
  
        if (city) this.city = city;
        if (country) this.country = country;
        if (method) this.calculationMethod = method;
      },
      saveSettings() {
        localStorage.setItem('city', this.city);
        localStorage.setItem('country', this.country);
        localStorage.setItem('calculationMethod', this.calculationMethod);
        this.$router.push('/');
      },
      getLocation() {
        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(position => {
            const lat = position.coords.latitude;
            const lon = position.coords.longitude;
  
            // Update city and country based on coordinates
            this.city = lat.toFixed(2);
            this.country = lon.toFixed(2);
            // Save coordinates in local storage
            localStorage.setItem('latitude', lat);
            localStorage.setItem('longitude', lon);
            localStorage.setItem('city', this.city);
            localStorage.setItem('country', this.country);
          });
        } else {
          alert('Geolocation is not supported by this browser.');
        }
      }
    }
  };
  </script>
  
  <style scoped>
  .form-container {
    width: 100%;
    max-width: 600px;
    margin: 20px auto;
    padding: 20px;
    background: #f0f0f0;
    border-radius: 8px;
  }
  
  .form-container input, .form-container select, .form-container button {
    width: 100%;
    padding: 10px;
    margin-top: 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  
  .form-container button {
    background-color: #4CAF50;
    color: white;
    cursor: pointer;
    margin-top: 20px;
  }
  
  .form-container button:hover {
    background-color: #45a049;
  }
  
  h2 {
    text-align: center;
  }
  </style>
  