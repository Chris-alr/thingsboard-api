<template>
  <div class="hello">
    
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data:{
    name: 'HelloWorld',
    props: {
    msg: String
  }
  },
  setup(){
    let temperatura= null;
    const token = localStorage.getItem('token')
    axios.get("http://iot.ceisufro.cl:8080/api/plugins/telemetry/DEVICE/bd2276a0-0ea1-11ee-b199-3d650e5455ce/values/timeseries", {
  headers: {
    'Content-Type': 'application/json',
    'x-authorization': `Bearer ${token}` 
  }
})
  .then(response => {
    console.log(response.data);
    temperatura = response.data.temperature.value
  })
  .catch(error => {
    console.error(error);
  });
  return{
    temperatura
  }
   }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
