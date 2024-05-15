<template>
    <div class="container">
      <div class="next-prayer">
        <h2>{{ nextPrayer.name }}</h2>
        <p>in {{ nextPrayer.timeRemaining }}</p>
        <p>{{ city }}</p>
        <p>Hijri date: {{ hijriDate }}</p>
      </div>
      <div v-for="(time, name) in prayerTimes" :key="name" class="prayer-time">
        <span>{{ name }}</span>
        <input
          v-if="['Fajr', 'Dhuhr', 'Asr', 'Maghrib', 'Isha'].includes(name)"
          type="checkbox"
          :checked="adhanEnabled[name]"
          @change="toggleAdhan(name)"
        />
        <span>{{ time }}</span>
      </div>
      <div v-if="showForm" class="form-container">
        <form @submit.prevent="fetchPrayerTimesByCity">
          <input v-model="city" placeholder="City" required />
          <input v-model="country" placeholder="Country" required />
          <select v-model="method" required>
            <option value="" disabled>Select Calculation Method</option>
            <option value="0">Shia Ithna-Ansari</option>
            <option value="1">University of Islamic Sciences, Karachi</option>
            <option value="2">Islamic Society of North America</option>
            <option value="3">Muslim World League</option>
            <option value="4">Umm Al-Qura University, Makkah</option>
            <option value="5">Egyptian General Authority of Survey</option>
            <option value="7">Institute of Geophysics, University of Tehran</option>
            <option value="8">Gulf Region</option>
            <option value="9">Kuwait</option>
            <option value="10">Qatar</option>
            <option value="11">Majlis Ugama Islam Singapura, Singapore</option>
            <option value="12">Union Organization islamic de France</option>
            <option value="13">Diyanet İşleri Başkanlığı, Turkey</option>
            <option value="14">Spiritual Administration of Muslims of Russia</option>
          </select>
          <button type="submit">Get Prayer Times</button>
        </form>
        <button @click="fetchPrayerTimesByLocation">Use My Location</button>
      </div>
    </div>
  </template>
  
  <script>
  import HijriDate from 'hijri-date/lib/safe';
  
  export default {
    data() {
      return {
        prayerTimes: {},
        nextPrayer: {},
        city: '',
        country: '',
        method: '',
        hijriDate: '',
        adhanEnabled: {
          Fajr   : false,
          Dhuhr  : false,
          Asr    : false,
          Maghrib: false,
          Isha   : false
        },
        showForm: true,
        loading: false
      };
    },
    mounted() {
      this.calculateHijriDate();
      if (process.client) {
        this.city = localStorage.getItem('city') || '';
        this.country = localStorage.getItem('country') || '';
        this.method = localStorage.getItem('method') || '';
        if (this.city && this.country && this.method) {
          this.showForm = false;
          this.fetchPrayerTimesByCity();
        }
        const savedAdhanEnabled = JSON.parse(localStorage.getItem('adhanEnabled'));
        if (savedAdhanEnabled) {
          this.adhanEnabled = savedAdhanEnabled;
        }
      }
    },
    methods: {
      async fetchPrayerTimesByCity() {
        if (!this.method) {
          alert('Please select a calculation method.');
          return;
        }
        this.loading = true;
        try {
          const response = await this.$axios.$get('/timingsByCity', {
            params: {
              city: this.city,
              country: this.country,
              method: this.method
            }
          });
          this.prayerTimes = response.data.timings;
          this.calculateNextPrayer();
          localStorage.setItem('city', this.city);
          localStorage.setItem('country', this.country);
          localStorage.setItem('method', this.method);
          this.showForm = false;
        } catch (error) {
          console.error('Error fetching prayer times:', error);
        } finally {
          this.loading = false;
        }
      },
      async fetchPrayerTimesByLocation() {
        if (!navigator.geolocation) {
          alert('Geolocation is not supported by your browser.');
          return;
        }
        this.loading = true;
        navigator.geolocation.getCurrentPosition(
          async position => {
            try {
              const { latitude, longitude } = position.coords;
              const response = await this.$axios.$get('/timings', {
                params: {
                  latitude,
                  longitude,
                  method: this.method
                }
              });
              this.prayerTimes = response.data.timings;
              this.calculateNextPrayer();
              this.city = response.data.meta.timezone;
              localStorage.setItem('city', this.city);
              localStorage.setItem('country', this.country);
              localStorage.setItem('method', this.method);
              this.showForm = false;
            } catch (error) {
              console.error('Error fetching prayer times:', error);
            } finally {
              this.loading = false;
            }
          },
          error => {
            console.error('Geolocation error:', error);
            this.loading = false;
          }
        );
      },
      calculateNextPrayer() {
        const now = new Date();
        const prayerTimesArray = Object.entries(this.prayerTimes).map(([name, time]) => ({
          name,
          time: new Date(`${now.toDateString()} ${time}`)
        }));
        const upcomingPrayers = prayerTimesArray.filter(prayer => prayer.time > now);
        if (upcomingPrayers.length > 0) {
          this.nextPrayer = upcomingPrayers[0];
        } else {
          const nextDayFirstPrayer = prayerTimesArray[0];
          nextDayFirstPrayer.time.setDate(nextDayFirstPrayer.time.getDate() + 1);
          this.nextPrayer = nextDayFirstPrayer;
        }
        this.nextPrayer.timeRemaining = this.calculateTimeRemaining(this.nextPrayer.time);
      },
      calculateTimeRemaining(prayerTime) {
        const now = new Date();
        const diffMs = prayerTime - now;
        const diffMins = Math.floor(diffMs / 1000 / 60);
        const hours = Math.floor(diffMins / 60);
        const mins = diffMins % 60;
        return `${hours}h ${mins}m`;
      },
      calculateHijriDate() {
        const hijri = new HijriDate();
        this.hijriDate = `${hijri.year}-${hijri.month + 1}-${hijri.date}`;
      },
      toggleAdhan(prayer) {
        this.adhanEnabled[prayer] = !this.adhanEnabled[prayer];
        localStorage.setItem('adhanEnabled', JSON.stringify(this.adhanEnabled));
      },
      playAdhan(prayer) {
        const audioSettings = JSON.parse(localStorage.getItem('audioSettings')) || {};
        const adhanFile = audioSettings[prayer];
        if (adhanFile) {
          const audio = new Audio(`path/to/your/audio/files/${adhanFile}`);
          audio.play();
        }
      }
    }
  };
  </script>
  
  
  <style scoped>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px;
    font-family: 'Arial, sans-serif';
  }
  
  .hijri-date {
    position: absolute;
    top: 10px;
    left: 10px;
    font-size: 1rem;
    color: #333;
  }
  
  .next-prayer {
    margin-bottom: 20px;
    text-align: center;
  }
  
  .next-prayer h2 {
    font-size: 2rem;
    color: #28a745;
  }
  
  .next-prayer p {
    font-size: 1rem;
    color: #555;
  }
  
  .prayer-time {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    max-width: 500px;
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
  
  .prayer-time .checkbox-container {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 0 20px; /* Adjust this value to properly align the checkbox */
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
  
  .form-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    max-width: 400px;
  }
  
  .form-container form {
    display: flex;
    flex-direction: column;
    width: 100%;
  }
  
  .form-container input,
  .form-container select,
  .form-container button {
    margin-bottom: 10px;
    padding: 10px;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  
  .form-container button {
    background-color: #28a745;
    color: #fff;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .form-container button:hover {
    background-color: #218838;
  }
  
  nav {
    display: flex;
    justify-content: center;
    background-color: #28a745;
    padding: 10px;
    width: 100%;
  }
  
  nav a {
    color: #fff;
    text-decoration: none;
    margin: 0 10px;
    font-size: 1rem;
  }
  
  nav a:hover {
    text-decoration: underline;
  }
  </style>
  