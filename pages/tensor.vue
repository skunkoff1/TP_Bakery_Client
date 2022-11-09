<template>
  <div class="custom-container">
    <header>
      <button type="button" class="btn" @click="runScript">Lancer le script</button>
    </header>
  </div>


</template>

<script>
import * as tf from '@tensorflow/tfjs';
import * as tfvis from '@tensorflow/tfjs-vis';

export default {
  data() {
    return {
      data: null,
    }
  },
  methods: {
    async runScript() {
      const request = await fetch('http://localhost:777/tensor', {
        method: 'GET',
        headers: {
          'content-type': 'application/json',
          }
      })
      const response = await request.json();
      const status = request.status;

      if(status == 200) {
        this.data = response.data;
        this.run(this.data);
      }
    },
    run(data) {
      const values = data.map(d => ({
        x: d.day,
        y: d.angbutter,
      }));

      tfvis.render.scatterplot(
        {name: 'day v sales'},
        {values},
        {
          xLabel: 'date',
          yLabel: 'sales',
          height: 300
        }
      );

    }
  }
}
</script>

<style scoped>
html, body, * {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
.custom-container {
  width: 90vw;
  height: 100vh;
  margin: auto;
}

.btn {
  background-color: rgb(38, 156, 185);
  color: white;
  padding: 10px;
  margin: 20px 80px 20px 5px;
}
</style>
