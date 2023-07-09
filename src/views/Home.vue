<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <h1>Variables de thingsboard </h1>
    <h1>temperature: {{ temperatura }}</h1>
    <h1>humedad:{{ humedad }}%</h1>
  </div>
</template>

<script>
import router from '../router';
import axios from 'axios'

export default {
  data() {
    return {
      token: '',
      refreshToken: '',
      lastRefreshTime: null,
      temperatura: '',
      humedad:''
    };
  },
  setup(){

  },
  created() {
    this.refreshToken = localStorage.getItem('refreshToken');
    this.lastRefreshTime = localStorage.getItem('lastRefreshTime');

    if (this.refreshToken && this.lastRefreshTime) {
      const currentTime = new Date().getTime();
      const timeSinceLastRefresh = currentTime - this.lastRefreshTime;

      const tokenExpirationTime = 1 * 60 * 1000; // 15 minutos en milisegundos

      if (timeSinceLastRefresh >= tokenExpirationTime) {
        this.refreshTokenRequest();
      }
    } else {
      // Realizar la solicitud inicial de login para obtener el token y refreshToken
      this.$router.push("/");
    }
    this.lastTelemetry();
  },
  methods: {
    async refreshTokenRequest() {
      const oldToken = localStorage.getItem('token');

      try {
        const response = await axios.post('http://iot.ceisufro.cl:8080/api/auth/token', {
          refreshToken: this.refreshToken
        }, {
          headers: {
            'Content-Type': 'application/json',
            'X-Authorization': `Bearer ${oldToken}`
          }
        });

        const { token, refreshToken } = response.data;

        // Actualizar los valores de token, refreshToken y lastRefreshTime en localStorage
        localStorage.setItem('token', token);
        localStorage.setItem('refreshToken', refreshToken);
        localStorage.setItem('lastRefreshTime', new Date().getTime());

        // Actualizar el token en las solicitudes Axios posteriores si es necesario

      } catch (error) {
        console.error(error);
        // Manejar el error de alguna forma
      }
    },
    async lastTelemetry() {
      const token = localStorage.getItem('token');
    axios.get("http://iot.ceisufro.cl:8080/api/plugins/telemetry/DEVICE/bd2276a0-0ea1-11ee-b199-3d650e5455ce/values/timeseries", {
      headers: {
        'Content-Type': 'application/json',
        'x-authorization': `Bearer ${token}`
      }
    })
    .then(response => {
      console.log(response.data);
      this.temperatura = response.data.temperature[0].value;
      this.humedad = response.data.humidity[0].value;
      console.log(this.temperatura);
      console.log(this.humedad)
    })
    .catch(error => {
      console.error(error);
    });
    }

  }
};
/*
export default {
  /*
  data() {
    return {
      temperatura: ''
    };
  },
  created() {
    const token = localStorage.getItem('token');
    axios.get("http://iot.ceisufro.cl:8080/api/plugins/telemetry/DEVICE/bd2276a0-0ea1-11ee-b199-3d650e5455ce/values/timeseries", {
      headers: {
        'Content-Type': 'application/json',
        'x-authorization': `Bearer ${token}`
      }
    })
    .then(response => {
      console.log(response.data);
      this.temperatura = response.data.temperature[0].value;
      console.log(this.temperatura);
    })
    .catch(error => {
      console.error(error);
    });
  }
}*/
</script>
