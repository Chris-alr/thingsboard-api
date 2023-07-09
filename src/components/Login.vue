<template>
    <form @submit.prevent="submitForm">
      <label>Email</label>
      <input v-model="email" type="email" required>
      <label>Contraseña</label>
      <input v-model="password" type="password" required>
      <button type="submit">Enviar</button>
    </form>
  </template>
  
  <script>
  import axios from 'axios';
  import router from '../router';
  
  export default {
    data() {
      return {
        email: '',
        password: ''
      };
    },
    methods: {
      async submitForm() {
        try {
          const response = await axios.post('http://iot.ceisufro.cl:8080/api/auth/login', {
            username: this.email,
            password: this.password
          });
  
          const { token, refreshToken } = response.data;
  
          localStorage.setItem('token', token);
          localStorage.setItem('refreshToken', refreshToken);
          localStorage.setItem('lastRefreshTime', new Date().getTime());
  

          // Realizar otras acciones después de guardar el token en localStorage
  
        } catch (error) {
          console.error(error);
          // Manejar el error de alguna forma
        }

        this.$router.push("/");
      }
    }
  };
  </script>
  