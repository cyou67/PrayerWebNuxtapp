<template>
    <div>
      <nav class="navbar">
        <NuxtLink to="/">Home</NuxtLink>
        <NuxtLink to="/settings">Settings</NuxtLink>
        <NuxtLink to="/audio-settings">Audio Settings</NuxtLink>
      </nav>
      <Nuxt />
    </div>
  </template>
  
  <style scoped>
  .navbar {
    display: flex;
    justify-content: center;
    background-color: #f8f9fa;
    padding: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .navbar a {
    margin: 0 15px;
    text-decoration: none;
    color: #333;
    font-weight: bold;
  }
  
  .navbar a:hover {
    color: #007bff;
  }
  </style>
  