<script>
import DB from "./DB.svelte";
import Details from "./Details.svelte";
import "@polymer/paper-card";
import "@material/mwc-button";
import "@material/mwc-icon-button";
import "@material/mwc-top-app-bar";
import { Button } from "@material/mwc-button";
import AjoutLivre from "./AjoutLivre.svelte";

let books = [];

let detailsLivre = null;

function afficheDetails(id_livre) {
  detailsLivre = id_livre;
}

function afficheAll() {
  detailsLivre = null;
}

function ajouterLivre() {
  detailsLivre = "ajoutLivre";
}

function modifLivre() {
  detailsLivre = "ajoutLivre";
}

</script>

<style>
.grid-container {
  display: grid;
  grid-template-columns: auto auto auto auto;
  padding: 1px;
}
.grid-item {
  background-color: rgba(255, 255, 255, 0.8);
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 20px;
  font-size: 30px;
  text-align: center;
}
.grid-item:hover  {
  background-color: orange;
}
mwc-top-app-bar {
  --mdc-theme-primary: orange;
  --mdc-theme-on-primary: black;
}
img{
  height: 9em;
  width: auto;
}
</style>

<link href="https://fonts.googleapis.com/css?family=Material+Icons&display=block" rel="stylesheet">
<main>

<mwc-top-app-bar>
  <div slot="title">Livres</div>
  <mwc-icon-button id="home" on:click={afficheAll} icon="home" slot="actionItems"></mwc-icon-button>
  <mwc-icon-button on:click={ajouterLivre} icon="add" slot="actionItems"></mwc-icon-button>
</mwc-top-app-bar>
{#if detailsLivre == null}
<DB bind:documents={books} initsrc="./books.json" collection="books"/>
<div class="grid-container">
  {#each books as book}
    <div on:click={afficheDetails(book._id)} class="grid-item">
      <img src="data:image/jpeg;base64, {book.img.data}" alt="{book.title}" title="{book.title}"/>
    </div>
    {/each}
  </div>
{:else if detailsLivre != null && detailsLivre != "ajoutLivre"}
  <Details id_livre={detailsLivre} collection="books"/>
{:else}
  <AjoutLivre collection="books"/>
{/if}
</main>