<template>
  <div class="custom-container">

    <!-- HEADER -->
    <header>
      <h1>Statistiques personnalisables</h1>
    </header>

    <!-- *************** DIV STATISTIQUES ********************** -->
    <div>
      <div class="form-div">
        <div class="up">
          <div class="left">
            <div class="form-input">
              <p>Jour</p>
              <b-form-select class="select" v-model="selected_day" :options="days"></b-form-select>
            </div>
            <div class="form-input">
              <p>Produit</p>
              <b-form-select class="select" v-model="selected_product" :options="products"></b-form-select>
            </div>
          </div>
          <div class="right">
            <div class="form-input">
              <p>Année</p>
              <b-form-select class="select" v-model="selected_year" :options="years"></b-form-select>
            </div>
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

      <!-- RESUME -->
      <h3 v-if="moyenne!=''">la moyenne des ventes est de : {{moyenne}}</h3>
      <h3 v-if="nbOfDay!=''">Nombre de jours de données : {{nbOfDay}}</h3>
      <h3 v-if="dayMax!=''">Journée de meilleure ventes : {{dayMax}}</h3>
      <h3 v-if="max!=''">vendu ce jour la : {{max}}</h3>
      <h3 v-if="aucun==true">Aucun {{selected_product}} vendu le {{display_day}}</h3>
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
      days: [{value:"Mon", text: "Lundi"}, {value: "Tues", text: "Mardi"},
      {value: "Wed", text:"Mercredi"}, {value: "Thur", text: "Jeudi"},
      {value: "Fri", text: "Vendredi"}, {value: "Sat", text: "Samedi" },
      {value: "Sun", text: "Dimanche"}],
      products: ["tous","angbutter", "plain_bread", "jam", "americano",
                "croissant", "caffe_latte", "tiramisu_croissant",
                "cacao_deep", "pain_au_chocolat", "almond_croissant",
                "croque_monsieur", "mad_garlic", "milk_tea", "gateau_chocolat",
                "pandoro", "cheese_cake", "lemon_ade", "orange_pound",
                "wiener", "vanila_latte", "berry_ade", "tiramisu", "merinque_cookies"],
      years: ["2019", "2020", "all"],
      places: ["mag1", "mag2", "mag3", "mag4", "mag5", "mag6",
              "mag7", "mag8", "mag9", "mag10", "mag11", "mag12",
              "mag13", "mag14", "mag15", "mag16", "mag17"],
      months: [{value:"01", text: "Janvier"},{value:"02", text: "Février"},{value:"03", text: "Mars"},
                {value:"04", text: "Avril"}, {value:"05", text: "Mai"}, {value:"06", text: "Juin"},
                {value:"07", text: "Juillet"}, {value:"08", text: "Août"}, {value:"09", text: "Septembre"},
                {value:"10", text: "Octobre"},{value:"11", text: "Novembre"},{value:"12", text: "Décembre"}],
      selected_day: "",
      selected_product: "",
      selected_year: "",
      selected_place: "",
      selected_month:"",
      moyenne: "",
      nbOfDay:"",
      dayMax: "",
      max:"",
      ctx: null,
      chart: null,
      aucun:false,
      display_day: '',
      loading: false,
    }
  },
  methods: {
    /*============= API STATISTIQUES =================== */
    async getStats() {

      this.loading = true;
      this.moyenne = "";
      this.nbOfDay = "";
      this.dayMax = "";
      this.max = "";
      this.aucun = false;

      let day = this.selected_day;
      let year = this.selected_year;
      let product = this.selected_product;
      let place = this.selected_place;

      const body = { day, year, product, place};

      const request = await fetch(process.env.API_DEV + "stats", {
        method: "POST",
          headers: {
          'content-type': 'application/json',
          },
          body: JSON.stringify(body),
      });

      const response = await request.json();
      const status = request.status;
      if(status == 200) {
        if(response.mean == null) {
          this.aucun = true;
          this.loading = false;
          switch(this.selected_day) {
            case 'Mon':
              this.display_day = "lundi";
              break;
            case 'Tues':
              this.display_day = "mardi";
              break;
            case 'Wed':
              this.display_day = "mercredi";
              break;
            case 'Thur':
              this.display_day = "jeudi";
              break;
            case 'Fri':
              this.display_day = "vendredi";
              break;
            case 'Sat':
              this.display_day = "samedi";
              break;
            case 'Sun':
              this.display_day = "dimanche";
              break;
          }
        }
        else {
          this.moyenne = response.mean;
          this.nbOfDay = response.nbOfDay;
          this.dayMax = response.dayMax;
          this.max = response.max;
          this.loading = false;
          this.createChart(response.number, response.date)
        }

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
              label: 'Vente par jour',
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
