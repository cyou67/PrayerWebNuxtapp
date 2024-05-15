<template>
    <nav class="navbar">
      <ul class="navbar-list">
        <li><router-link to="/">Home</router-link></li>
        <li><router-link to="/settings">Settings</router-link></li>
      </ul>
    </nav>
  </template>
  
  <script>
  export default {
    name: 'Navbar'
  };
  </script>
  
  <style scoped>
  .navbar {
    background-color: #333;
    padding: 10px;
    display: flex;
    justify-content: center;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  .navbar-list {
    list-style-type: none;
    margin: 0;
    padding: 0;
    display: flex;
  }
  .navbar-list li {
    margin: 0 15px;
  }
  .navbar-list a {
    color: white;
    text-decoration: none;
    font-size: 18px;
  }
  .navbar-list a:hover {
    text-decoration: underline;
  }
  </style>
  