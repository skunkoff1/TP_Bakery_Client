<template>
  <div class="custom-container">

    <!-- HEADER -->
    <header>
      <h1>Predicition de vente par {{display}}</h1>
      <div class="up1">
        <button class="btn purple" type="button" @click="goToMonth">Mois</button>
        <button class="btn" type="button" @click="goToDay">Jour</button>
      </div>
    </header>

    <!-- *************** DIV STATISTIQUES ********************** -->
    <div v-if="display=='mois'">
      <div class="form-div purple">
        <div class="up">
          <div class="left">
            <div class="form-imput">
              <p>Mois</p>
              <b-form-select class="select" v-model="selected_month" :options="months"></b-form-select>
            </div>
            <div class="form-imput">
              <p>Produit</p>
              <b-form-select class="select" v-model="selected_product" :options="products"></b-form-select>
            </div>
          </div>
          <div class="right">
            <div class="form-imput">
              <p>Magasin</p>
              <b-form-select class="select" v-model="selected_place" :options="places"></b-form-select>
            </div>
          </div>
        </div>
        <div class="down">
          <button class="btn" type="button" @click="goToMonthPrediction">Faire une prédiction</button>
        </div>
      </div>

      <!-- RESUME -->
      <h3 v-if="aucun == true">pas de donnés pour le mois de {{display_month}}, prédiction impossible</h3>
      <h3 v-if="prediction!=''">Prediction : {{prediction}}</h3>
      <!-- COMPOSANT LOADING -->
      <div v-if="loading == true" class="loading">
        <div class="lds-roller"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      </div>
    </div>

    <!-- *************** DIV PREDICTION ********************** -->
    <div v-if="display=='jour'">
      <div class="form-div blue">
        <div class="up">
          <div class="left">
            <div class="form-imput">
              <p>Jour</p>
              <b-form-select class="select" v-model="selected_day" :options="days"></b-form-select>
            </div>
            <div class="form-imput">
              <p>Produit</p>
              <b-form-select class="select" v-model="selected_product" :options="products"></b-form-select>
            </div>
          </div>
          <div class="right">
            <div class="form-imput">
              <p>Magasin</p>
              <b-form-select class="select" v-model="selected_place" :options="places"></b-form-select>
            </div>
          </div>
        </div>
        <div class="down">
          <button class="btn purple" type="button" @click="goToDayPrediction">Faire une prédiction</button>
        </div>
      </div>

      <!-- RESUME -->
      <h3 v-if="aucun == true">pas de donnés pour le mois de {{display_day}}</h3>
      <h3 v-if="prediction != ''">Prediction : {{prediction}}</h3>
      <!-- COMPOSANT LOADING -->
      <div v-if="loading == true" class="loading">
        <div class="lds-roller"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      </div>

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
      places: ["mag1", "mag2", "mag3", "mag4", "mag5", "mag6",
              "mag7", "mag8", "mag9", "mag10", "mag11", "mag12",
              "mag13", "mag14", "mag15", "mag16", "mag17"],
      months: [{value:"01", text: "Janvier"},{value:"02", text: "Février"},{value:"03", text: "Mars"},
                {value:"04", text: "Avril"}, {value:"05", text: "Mai"}, {value:"06", text: "Juin"},
                {value:"07", text: "Juillet"}, {value:"08", text: "Août"}, {value:"09", text: "Septembre"},
                {value:"10", text: "Octobre"},{value:"11", text: "Novembre"},{value:"12", text: "Décembre"}],
      selected_day: "",
      selected_product: "",
      selected_place: "",
      selected_month:"",
      display: "mois",
      prediction: "",
      aucun: false,
      display_month:"",
      display_day: '',
      loading: false,
    }
  },
  methods: {
       /*============= API PREDICTION MOIS=================== */
    async goToMonthPrediction() {
      this.loading = true;
      let month = this.selected_month;
      let product = this.selected_product;
      let place = this.selected_place;
      this.aucun = false;
      this.prediction = "";

      const body = { month, product, place};

      const request = await fetch(process.env.API_DEV + "monthPrediction", {
        method: "POST",
          headers: {
          'content-type': 'application/json',
          },
          body: JSON.stringify(body),
      });

      const response = await request.json();
      const status = request.status;
      this.loading = false;
      if(status == 200) {
        if(response.mean == null) {
          this.aucun = true;
          switch(this.selected_month) {
            case '01':
              this.display_month = "janvier"
              break;
            case '02':
              this.display_month = "février"
              break;
            case '03':
              this.display_month = "mars"
              break;
            case '04':
              this.display_month = "avril"
              break;
            case '05':
              this.display_month = "mai"
              break;
            case '06':
              this.display_month = "juin"
              break;
            case '07':
              this.display_month = "juillet"
              break;
            case '08':
              this.display_month = "août"
              break;
            case '09':
              this.display_month = "septembre"
              break;
            case '10':
              this.display_month = "octobre"
              break;
            case '11':
              this.display_month = "novembre"
              break;
            case '12':
              this.display_month = "décembre"
              break;
          }
        }
        else {
          this.prediction = response.result;
        }
      }
    },
    /*============= API PREDICTION Jour=================== */
    async goToDayPrediction() {
      this.loading = true;
      let day = this.selected_day;
      let product = this.selected_product;
      let place = this.selected_place;
      this.aucun = false;
      this.prediction = "";

      const body = { day, product, place};

      const request = await fetch(process.env.API_DEV + "dayPrediction", {
        method: "POST",
          headers: {
          'content-type': 'application/json',
          },
          body: JSON.stringify(body),
      });

      const response = await request.json();
      const status = request.status;
      this.loading = false;
      if(status == 200) {
        console.log(response)
        if(response.mean == null) {
          this.aucun = true;
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
          this.prediction = response.result;
        }
      }
    },
    /*===================== NAVIGATION ====================*/
    goToMonth() {
      this.display = "mois";
      this.aucun = false;
      this.prediction = "";
    },
    goToDay() {
      this.display = "jour";
      this.aucun = false;
      this.prediction = "";
    }
  },
}


</script>

<style scoped>

.form-div {
  width: 100%;
  display: flex;
  flex-direction: column;
  background-color: rgb(189, 255, 255);
  border-radius: 5px;
}

.blue {
  background-color: var(--blue);
}

.up {
  display: flex;
  justify-content: space-between;
}

.up1 {
  display: flex;
  justify-content: flex-start;
  align-items: center;
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
}

.btn {
  background-color: var(--blue);
  color: white;
  padding: 10px;
  margin: 20px 80px 20px 0px;
}

.purple {
  background-color: var(--purple);
}

p {
  margin: 5px 20px;
}

.down {
  display: flex;
  justify-content: flex-end;
}

.form-imput {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
</style>
