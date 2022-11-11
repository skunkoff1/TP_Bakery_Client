<template>
  <div class="custom-container">

    <!-- HEADER -->
    <header>
      <div class="left">
        <img src="../assets/image/boulangerie.jpg" class="head-img">
      </div>
      <div class="right">
        <h1>- Compte rendu, analyse et prédiction des ventes d'une chaîne de boulangerie coréenne. </h1>
        <h1>- Utilisation de TensorFlow afin d'analyser des données et créer un modèle de prédiction. </h1>
        <h3>Damien Didrich 2022 - ESI 2022 - TP Intelligence Artificielle</h3>
      </div>
    </header>

    <div class="para">
      <div class="left1">
        <h2>1. Création de l'API</h2>
        <p>Pour ce TP, j'ai crée et déployé une API pour laquelle, j'ai mis en place <a href="https://api.bakery.dd-web.fr/doc" target="_blank">une documentation assez sommaire.</a></p>
        <p>Cette Api est développé en NodeJs et ne respecte pas l'architecture REST par choix de rapidité de développement et faciliter la communication avec ce présent site.</p>
        <p>Elle est divisé en 2 contrôleurs, l'un étant dédié purement aux statistiques de ventes et le second aux prédictions de ventes.</p>
      </div>
      <div class="right1">
        <img class="img" src="../assets/image/API1.png">
      </div>
    </div>


    <div class="para">
      <div class="left1">
        <h2>2. Les statistiques :</h2>
          <p>Le but de l'API, concernant la partie statistiques, est de renvoyer les données correspondant aux critères demandés. Par exemple, les ventes de croque-monsieur le lundi pour
            l'un des 17 magasins de la chaîne, ou les ventes sur l'année de tous les produits d'un des magasins ou encore, les ventes de angbutter sur l'année de tous
            les magasins de la chaîne.</p>
          <p>Le temps de développement étant assez court, je n'ai pas prévu toutes les possibilités de statistiques avec les données
            à notre disposition. Néanmoins, les possibilités offertes sont déjà nombreuses.</p>
        <p>La première chose à faire avec les données dans mon cas fut de convertir le fichier, à l'origine au format <b>.csv</b>, au format <b>.json</b>.
          La manipulation des données y est plus simple dans le cadre d'une API en NodeJS utilisant le Javascript comme langage.</p>
          <p>Je reconnais que le langage <b>python</b> et ses nombreuses librairies dédiées au traitement de données telles que <b>panda</b>,
             est bien plus adapté à ce genre de tâche, mais le délai de développement étant assez court,
            j'ai pris le choix de ne pas apprendre à développer une API en python afin de ne pas perdre de temps avec un apprentissage supplémentaire et rester sur
             une technologie que je connais et maîtrise.</p>
             <p>L'API se charge donc de renvoyer les données demandées et le présent site, développé en <b>NuxtJs</b>, un framework de <b>VueJs</b>, se charge de les représenter
            de façon intelligible sous forme de graphes en utilisant la librairie <b>chartJs.</b></p>
      </div>
      <div class="right1">
        <img class="img" src="../assets/image/stats1.png">
      </div>
    </div>

    <div class="para">
      <div class="left1">
        <h2>3. Les prédictions :</h2>
          <p>Concernant la partie prédictions, l'API effectue plusieurs tâches :
            <ul>
              <li>elle récolte tout d'abord les données correspondant aux critères de prédictions demandés</li>
              <li>elle traite ensuite ces données, entraine et crée un modèle de prédiction en utilisant la librairie <b>brain.Js</b></li>
              <li>enfin, elle envoie la prédiction à ce présent site qui se charge de l'afficher</li>
            </ul>
          </p>
          <p>Les critères possibles pour une prédiction de ventes sont sur un jour de la semaine ou un mois de l'année, pour un produit donné ou tous, et enfin pour un magasin donné.</p>
          <p>Les données ne couvrant que la période du 12 juillet 2019 au 1er mai 2020 et n'étant, à priori pas complètes puisque beaucoup de jour sont manquants, il est difficile de
            définir une tendance et les prédictions sont souvent très proches d'une simple moyenne.
          </p>
          <p>Les données n'étant pas conséquentes, l'entrainement et la création du modèle sont réalisés en temps réel, à chaque demande.
          </p>
          <p>Dans l'idéal, il aurait été préférable d'entrainer l'IA et de créer le modèle au préalable afin d'accélérer chaque traitement et demande de prédiction.
            J'ai pris le parti de ne pas le faire car, comme évoqué avant, le traitement dans ce cas précis prend un temps raisonnable et cela permet de tester
            directement plusieurs scénarios de prédictions.</p>
      </div>
      <div class="right1">
        <img class="img" src="../assets/image/pred1.png">
        <img class="img" src="../assets/image/pred2.png">
      </div>
    </div>

    <div class="para">
      <div class="left1">
        <h2>4. Tensor Flow :</h2>
          <p>
          </p>

      </div>
      <div class="right1">
        <img class="img" src="../assets/image/pred1.png">
        <img class="img" src="../assets/image/pred2.png">
      </div>
    </div>


    <div class="footer">
      <p>&copy; 2022 Didrich Damien</p>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {

    }
  },
  methods: {
  }
}


</script>

<style scoped>
header, .para {
  margin:auto;
}

header {
  display: flex;
  justify-content: space-between;
}

.para {
  margin-top: 20px;
  display: flex;
}

h2, h4 {
  padding: 10px 0;
  text-decoration: underline;
}

.left, .right {
  width: 48%;
}

.left1 {
  width:60%;
}

.right1 {
  width: 40%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.head-img {
  width: 100%;
  margin:auto;
}

.img {
  width: 90%;
  margin: auto;
}

.footer {
  height: 200px;
  display: flex;
  justify-content: flex-end;
  align-items: flex-end;
}
</style>
