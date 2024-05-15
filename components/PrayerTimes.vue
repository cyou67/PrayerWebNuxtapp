<template>
    <div class="prayer-times">
      <div class="top-bar">
        <div class="next-prayer">
          <span class="next-prayer-name">{{ nextPrayer.name }}</span>
          <span class="next-prayer-time">in {{ nextPrayer.time }}</span>
        </div>
        <div class="location-hijri">
          <span class="city">{{ city }}</span>
          <span class="hijri-date">Hijri date: {{ hijriDate }}</span>
        </div>
      </div>
      <div v-for="prayer in prayerTimes" :key="prayer.name" class="prayer-time">
        <span class="prayer-name">{{ prayer.name }}</span>
        <div class="checkbox-container" v-if="['Fajr', 'Dhuhr', 'Asr', 'Maghrib', 'Isha'].includes(prayer.name)">
          <input type="checkbox" v-model="adhanSettings[prayer.name]" @change="saveAdhanSettings(prayer.name)">
        </div>
        <span class="prayer-time-value">{{ prayer.time }}</span>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        prayerTimes: [],
        city: '',
        hijriDate: '',
        nextPrayer: {
          name: '',
          time: ''
        },
        adhanSettings: {
          Fajr: false,
          Dhuhr: false,
          Asr: false,
          Maghrib: false,
          Isha: false
        }
      };
    },
    mounted() {
      this.loadPrayerTimes();
      this.loadAdhanSettings();
    },
    methods: {
      loadPrayerTimes() {
        // Your existing logic to load prayer times
      },
      loadAdhanSettings() {
        const settings = JSON.parse(localStorage.getItem('adhanSettings')) || this.adhanSettings;
        this.adhanSettings = settings;
      },
      saveAdhanSettings(prayer) {
        localStorage.setItem('adhanSettings', JSON.stringify(this.adhanSettings));
      }
    }
  };
  </script>
  
  <style scoped>
  .prayer-times {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px;
    background: #fff;
  }
  
  .top-bar {
    width: 100%;
    max-width: 600px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
  }
  
  .next-prayer {
    text-align: center;
    font-size: 1.2rem;
  }
  
  .next-prayer-name {
    font-weight: bold;
    color: #28a745;
  }
  
  .next-prayer-time {
    color: #666;
  }
  
  .location-hijri {
    text-align: right;
  }
  
  .city {
    font-size: 1rem;
    color: #333;
  }
  
  .hijri-date {
    font-size: 0.9rem;
    color: #888;
  }
  
  .prayer-time {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    max-width: 600px;
    padding: 10px;
    margin: 5px 0;
    background-color: #f9f9f9;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .prayer-time span {
    font-size: 1.2rem;
    color: #333;
  }
  
  .prayer-name {
    flex: 1;
    text-align: left;
  }
  
  .checkbox-container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 40px;
  }
  
  .prayer-time-value {
    flex: 1;
    text-align: right;
  }
  
  .prayer-time input[type='checkbox'] {
    appearance: none;
    width: 20px;
    height: 20px;
    border: 2px solid #28a745;
    border-radius: 50%;
    background-color: #fff;
    cursor: pointer;
    position: relative;
  }
  
  .prayer-time input[type='checkbox']:checked::after {
    content: '';
    position: absolute;
    top: 2px;
    left: 2px;
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background-color: #28a745;
  }
  </style>
  