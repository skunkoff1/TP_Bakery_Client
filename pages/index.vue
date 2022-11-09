<template>
  <div class="custom-container">

    <!-- HEADER -->
    <header>
      <div class="left">
        <img src="../assets/image/boulangerie.jpg" class="head-img">
      </div>
      <div class="right">
        <h1>Compte rendu, analyse et prédiction des ventes <br>d'une chaîne de boulangerie coréenne </h1>
      </div>
    </header>

    <div class="para">

    </div>

  </div>
</template>

<script>

export default {
  data() {
    return {
      days: [{value:"Mon", text: "Lundi"}, {value: "Tues", text: "Mardi"},
      {value: "Wed", text:"Mercredi"}, {value: "Thur", text: "Jeudi"},
      {value: "Fri", text: "Vendredi"}, {value: "Sat", text: "Samedi" },
      {value: "Sun", text: "Dimanche"}],
      products: ["angbutter", "plain_bread", "jam", "americano",
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
      nbOfMonth: "",
      ctx: null,
      chart: null,
      display: "Statistiques",
      prediction: "",
    }
  },
  methods: {
  }
}


</script>

<style scoped>
header, .para {
  width: 90%;
  margin:auto;
}

header {
  display: flex;
  justify-content: space-between;
}

.left, .right {
  width: 45%;
}

.head-img {
  width: 100%;
  margin:auto;
}
</style>
