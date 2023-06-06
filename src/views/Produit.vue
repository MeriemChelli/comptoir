<template>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css" />

  <main>
    <div>
      <table>
        <caption>Les produits - page {{ data.page.number + 1 }} / {{ data.page.totalPages }} </caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <!-- Si le tableau des produits est vide -->
        <tr v-if="data.listeProduits.length === 0">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des produits n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
      </table>
      <div class="pagination">
        <button @click="ImportProduits(0)">
          &#9198;<!-- Icône pour le début -->
        </button>
        <button v-if="data.page.number + 1 > 1" @click="ImportProduits(data.page.number - 1)">
          &#8592; <!-- Icône pour le précédent -->
        </button>
        <button v-if="data.page.number + 1 < data.page.totalPages" @click="ImportProduits(data.page.number + 1)">
          &#8594;
        </button>
        <button @click="ImportProduits(data.page.totalPages - 1)">
          &#9197;
        </button>
      </div>
è
    </div>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest, APIError } from "../api";
let data = reactive({
  // La liste des produits affichée sous forme de table
  listeProduits: [],
  // Informations de la page
  page: {}
});
function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}

function ImportProduits(numdepage) {
  doAjaxRequest(`/api/produits?page=${numdepage}&size=5&sort=nom,asc`)
      .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.page = json.page;
      })
      .catch(showError);
}
// A l'affichage du composant, on affiche la liste des produits de la 1ère page
onMounted(() => {
  ImportProduits(0);
});
</script>


<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}
th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #3f51b5; /* Bleu foncé */
  color: #fff; /* Texte blanc */
}
table {
  margin-top: 20px;
  border-collapse: collapse;
  width: 100%;
}
.pagination {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}
.pagination button {
  background-color: #2196f3; /* Bleu */
  color: #fff; /* Texte blanc */
  border: none;
  border-radius: 4px;
  padding: 8px 16px;
  margin: 0 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}
.pagination button:hover {
  background-color: #1976d2; /* Bleu foncé au survol */
}
.pagination button:disabled {
  background-color: #ccc; /* Gris clair */
  cursor: not-allowed;
}
.pagination button:first-child,
.pagination button:last-child {
  background-color: #555555; /* Gris foncé pour le premier et le dernier bouton */
}
.pagination button:first-child:hover,
.pagination button:last-child:hover {
  background-color: #4d4d4d; /* Gris foncé au survol */
}
</style>
