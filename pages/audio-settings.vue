<template>
    <div class="audio-settings-container">
      <h1>Audio Settings</h1>
      <form @submit.prevent="saveAudioSettings">
        <div v-for="prayer in prayers" :key="prayer" class="prayer-audio-setting">
          <label :for="prayer">{{ prayer }}</label>
          <select v-model="audioSettings[prayer]" class="input-field" :id="prayer">
            <option value="">None</option>
            <option value="mishary1.mp3">Mishary 1</option>
            <option value="mishary2.mp3">Mishary 2</option>
            <option value="SalahMansoorAz-Zahrani.mp3">Salah Mansoor Az-Zahrani</option>
          </select>
        </div>
        <button type="submit" class="submit-btn">Save Audio Settings</button>
      </form>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        prayers: ['Fajr', 'Dhuhr', 'Asr', 'Maghrib', 'Isha'],
        audioSettings: {
          Fajr: '',
          Dhuhr: '',
          Asr: '',
          Maghrib: '',
          Isha: ''
        }
      };
    },
    mounted() {
      if (process.client) {
        this.audioSettings = JSON.parse(localStorage.getItem('audioSettings')) || {
          Fajr: '',
          Dhuhr: '',
          Asr: '',
          Maghrib: '',
          Isha: ''
        };
      }
    },
    methods: {
      saveAudioSettings() {
        localStorage.setItem('audioSettings', JSON.stringify(this.audioSettings));
        alert('Audio settings saved successfully!');
      }
    }
  };
  </script>
  
  <style scoped>
  .audio-settings-container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    background: #f9f9f9;
    border-radius: 10px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
    text-align: center;
  }
  
  .input-field {
    margin: 10px 0;
    padding: 10px;
    font-size: 16px;
    border: 1px solid #ddd;
    border-radius: 5px;
    width: 100%;
  }
  
  .submit-btn {
    margin: 10px 0;
    padding: 10px;
    font-size: 16px;
    color: #fff;
    background-color: #5cb85c;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .submit-btn:hover {
    background-color: #4cae4c;
  }
  
  .prayer-audio-setting {
    margin-bottom: 15px;
  }
  
  .prayer-audio-setting label {
    display: block;
    font-weight: bold;
    margin-bottom: 5px;
  }
  </style>
  