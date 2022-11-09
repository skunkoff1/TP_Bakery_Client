<template>
  <div class="custom-container">

    <!-- HEADER -->
    <header>
      <h1>Statistiques sur l'année</h1>
    </header>

    <!-- *************** DIV STATISTIQUES ********************** -->
    <div>
      <div class="form-div">
        <div class="up">
          <div class="left">
            <div class="form-input">
              <p>Produit</p>
              <b-form-select class="select" v-model="selected_product" :options="products"></b-form-select>
            </div>
          </div>
          <div class="right">
            <div class="form-input">
              <p>Magasin</p>
              <b-form-select class="select" v-model="selected_place" :options="places"></b-form-select>
            </div>
          </div>
        </div>
        <div class="down">
          <button class="btn" type="button" @click="getStats">Voir les stats</button>
        </div>
      </div>

      <div v-if="loading == true" class="loading">
        <div class="lds-roller"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      </div>

      <!-- TABLEAU -->
      <canvas id="myChart" width="400" height="400"></canvas>
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';
export default {
  data() {
    return {
      products: ["tous","angbutter", "plain_bread", "jam", "americano",
                "croissant", "caffe_latte", "tiramisu_croissant",
                "cacao_deep", "pain_au_chocolat", "almond_croissant",
                "croque_monsieur", "mad_garlic", "milk_tea", "gateau_chocolat",
                "pandoro", "cheese_cake", "lemon_ade", "orange_pound",
                "wiener", "vanila_latte", "berry_ade", "tiramisu", "merinque_cookies"],
      places: ["mag1", "mag2", "mag3", "mag4", "mag5", "mag6",
              "mag7", "mag8", "mag9", "mag10", "mag11", "mag12",
              "mag13", "mag14", "mag15", "mag16", "mag17"],
      selected_product: "",
      selected_place: "",
      ctx: null,
      chart: null,
      aucun:false,
      loading: false,
    }
  },
  methods: {
    /*============= API STATISTIQUES =================== */
    async getStats() {

      this.loading = true;
      this.aucun = false;

      let product = this.selected_product;
      let place = this.selected_place;

      const body = { product, place};

      const request = await fetch(process.env.API_DEV + "getTotal", {
        method: "POST",
          headers: {
          'content-type': 'application/json',
          },
          body: JSON.stringify(body),
      });

      const response = await request.json();
      const status = request.status;

      if(status == 200) {
        console.log(response);
        this.createChart(response.totalArray, response.monthArray)
        this.loading = false;
      }
    },
    createChart(number, date) {
    this.ctx = document.getElementById('myChart').getContext('2d');
    if(this.chart != null) {
      this.chart.destroy();
    }
    this.chart = new Chart(this.ctx, {
      type: 'line',
      data: {
          labels: date,
          datasets: [{
              label: 'Vente par mois',
              data: number,
              backgroundColor: [
                  'rgba(255, 99, 132, 0.2)',
                  'rgba(54, 162, 235, 0.2)',
                  'rgba(255, 206, 86, 0.2)',
                  'rgba(75, 192, 192, 0.2)',
                  'rgba(153, 102, 255, 0.2)',
                  'rgba(255, 159, 64, 0.2)'
              ],
              borderColor: [
                  'rgba(255, 99, 132, 1)',
                  'rgba(54, 162, 235, 1)',
                  'rgba(255, 206, 86, 1)',
                  'rgba(75, 192, 192, 1)',
                  'rgba(153, 102, 255, 1)',
                  'rgba(255, 159, 64, 1)'
              ],
              borderWidth: 1
          }]
      },
      options: {
          scales: {
              y: {
                  beginAtZero: true
              }
          }
      }
    });
    },
  },
}


</script>

<style scoped>
.form-div {
  width: 100%;
  display: flex;
  flex-direction: column;
  background-color: var(--blue2);
  border-radius: 5px;
  color: var(--black);
}

.up {
  display: flex;
  justify-content: space-between;
}

h4 {
  margin-right: 50px;
}

.left, .right {
  width: 45%;
}

.select {
  width: 80%;
  margin: 5px 20px;
  padding: 5px;
}

.btn {
  background-color: rgb(38, 156, 185);
  color: white;
  padding: 10px;
  margin: 20px 80px 20px 5px;
}

p {
  margin: 5px 20px;
}

.down {
  display: flex;
  justify-content: flex-end;
}

.form-input {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
</style>
