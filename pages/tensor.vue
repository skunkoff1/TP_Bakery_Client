<template>
  <div class="custom-container">
    <header>
      <p>Les données utilisées sont les prix des Ford Focus en occasion
        <br>J'ai choisi d'utiliser ce dataset car les ventes en boulangerie
        n'était pas suffisamment significatives, ni structurées selon un modèle.
        <br>Ici, les données sont simples, simplement l'année et le prix de vente.
        Le kilométrage, le modèle ou la motorisation ne rentrent pas en compte.</p>
      <button type="button" class="btn" @click="runScript">Lancer l'entrainement du modèle</button>
    </header>

    <div class="para">
      <div v-if="loading == true" class="loading">
        <div class="lds-roller"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      </div>
      <div id="summary">
        <p>Résumé du modèle</p>
      </div>
      <div id="flex-up">
        <div class="left">
          <p>Données</p>
          <p>Jour / Ventes </p>
          <div id="dataDisplay">

          </div>
        </div>
        <div class="center">
          <p id="state">Entrainement</p>
          <div id="dataTrain">

          </div>
        </div>
        <div class="right">
          <p>Prédiction du modèle</p>
          <div id="dataPrediction">

          </div>
        </div>
      </div>
    </div>
  </div>




</template>

<script>
// import * as tf from '@tensorflow/tfjs';
// import * as tfvis from '@tensorflow/tfjs-vis';


export default {
  data() {
    return {
      data: null,
      loading:false,
    }
  },
  methods: {
    async runScript() {
      this.loading = true;
      const request = await fetch(process.env.API_DEV + 'tensor', {
        method: 'GET',
        headers: {
          'content-type': 'application/json',
          }
      })
      const response = await request.json();
      const status = request.status;

      if(status == 200) {
        this.loading = false;
        this.data = response.data;
        this.run(this.data);
      }
    },
    async run(data) {
      const tfvis = require('@tensorflow/tfjs-vis');
      const summary = document.getElementById('summary');
      summary.style.display = "flex";
      const dataDiv = document.getElementById('flex-up');
      const dataDisplay = document.getElementById('dataDisplay');
      dataDiv.style.display = "flex";
      const state = document.getElementById('state');
      state.innerHTML = "Entrainement du modèle en cours ...";

      const values = data.map(d => ({
        x: d.year,
        y: d.price,
      }));

      const model = this.createModel();
      tfvis.show.modelSummary(summary, model);


      tfvis.render.scatterplot(
        dataDisplay,
          {values},
          {
            xLabel: 'year',
            yLabel: 'price',
            height: 300,
            width: 500,
            xAxisDomain: [2000, 2021]
          }

      );

      const tensorData = this.convertToTensor(data);
      const {inputs, labels} = tensorData;

      // Train the model
      await this.trainModel(model, inputs, labels);
      state.innerHTML = "Entrainement du modèle terminé";

      this.testModel(model, data, tensorData);

    },
    createModel() {
      const tf = require('@tensorflow/tfjs');
      const model = tf.sequential();

      model.add(tf.layers.dense({inputShape: [1], units: 2, useBias: false}));
      model.add(tf.layers.dense({units: 10, activation: 'softmax'}));
      model.add(tf.layers.dense({units: 1, useBias: true}));
      model.add(tf.layers.dense({units: 1, activation: 'sigmoid'}));

      return model;
    },
    convertToTensor(data) {
    // Wrapping these calculations in a tidy will dispose any
    // intermediate tensors.

    const tf = require('@tensorflow/tfjs');

      return tf.tidy(() => {
      // Step 1. Shuffle the data
      tf.util.shuffle(data);

      // Step 2. Convert data to Tensor
      const inputs = data.map(d => d.year)
      const labels = data.map(d => d.price);

      const inputTensor = tf.tensor2d(inputs, [inputs.length, 1]);
      const labelTensor = tf.tensor2d(labels, [labels.length, 1]);

      //Step 3. Normalize the data to the range 0 - 1 using min-max scaling
      const inputMax = inputTensor.max();
      const inputMin = inputTensor.min();
      const labelMax = labelTensor.max();
      const labelMin = labelTensor.min();

      const normalizedInputs = inputTensor.sub(inputMin).div(inputMax.sub(inputMin));
      const normalizedLabels = labelTensor.sub(labelMin).div(labelMax.sub(labelMin));

      return {
        inputs: normalizedInputs,
        labels: normalizedLabels,
        // Return the min/max bounds so we can use them later.
        inputMax,
        inputMin,
        labelMax,
        labelMin,
      }
    });
  },
  async trainModel(model, inputs, labels) {
    const tf = require('@tensorflow/tfjs');
    const tfvis = require('@tensorflow/tfjs-vis');

    const div = document.getElementById('dataTrain');
    // Prepare the model for training.
    model.compile({
      optimizer: tf.train.adam(),
      loss: tf.losses.meanSquaredError,
      metrics: ['mse'],
    });

    const batchSize = 32;
    const epochs = 50;

    return await model.fit(inputs, labels, {
      batchSize,
      epochs,
      shuffle: true,
      callbacks: tfvis.show.fitCallbacks(
        div,
        ['loss', 'mse'],
        { height: 200, width: 500,callbacks: ['onEpochEnd'] }
        )
      });
    },
    testModel(model, inputData, normalizationData) {
      const tf = require('@tensorflow/tfjs');
      const tfvis = require('@tensorflow/tfjs-vis');
      const {inputMax, inputMin, labelMin, labelMax} = normalizationData;
      const dataPrediction = document.getElementById('dataPrediction');

      // Generate predictions for a uniform range of numbers between 0 and 1;
      // We un-normalize the data by doing the inverse of the min-max scaling
      // that we did earlier.
      const [xs, preds] = tf.tidy(() => {

        const xs = tf.linspace(0, 1, 100);
        const preds = model.predict(xs.reshape([100, 1]));

        const unNormXs = xs
          .mul(inputMax.sub(inputMin))
          .add(inputMin);

        const unNormPreds = preds
          .mul(labelMax.sub(labelMin))
          .add(labelMin);

        // Un-normalize the data
        return [unNormXs.dataSync(), unNormPreds.dataSync()];
      });

      const predictedPoints = Array.from(xs).map((val, i) => {
        return {x: val, y: preds[i]}
      });

      const originalPoints = inputData.map(d => ({
        x: d.year, y: d.price,
      }));

      tfvis.render.scatterplot(
        dataPrediction,
        {values: [originalPoints, predictedPoints], series: ['original', 'predicted']},
        {
          xLabel: 'year',
          yLabel: 'price',
          height: 300,
          width: 500,
          xAxisDomain: [2000, 2021]
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

header {
  display: flex;
}

.btn {
  background-color: rgb(38, 156, 185);
  color: white;
  padding: 10px;
  margin: 20px 80px 10px 5px;
}

.para {
  display: flex;
  flex-direction: column;
  width: 70vw;
}

#summary {
  display: none;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-top: 0px;
}

#flex-up {
  display: none;
  justify-content: flex-start;
  width: 90vw;
}

.center, .right {
  margin-left: 40px;
}

.left, .center, .right {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 10px;
}

p {
  padding: 20px;
}
</style>
