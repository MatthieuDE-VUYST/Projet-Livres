<script>
import { onMount } from 'svelte';
import PouchDB from 'pouchdb-browser';

export let collection;
export let id_livre;
let db;

onMount(
  async () => {
    const pdb = new PouchDB(collection);
    await pdb.info(); // Notre BD est disponible
    db = pdb; // db devient non nulle et donc déclenche load_books
  }
);

async function supprimer() {
  if (!db) return;

  db.get(id_livre).then(function(doc) {
    return db.remove(doc);
  }).then(function (result) {
    document.getElementById("home").click();
    console.log(result);
  }).catch(function (err) { 
    console.log(err);
  });
}

</script>
<style>
mwc-button {
  --mdc-theme-primary: black;
  --mdc-theme-on-primary: orange;
}

</style>

<mwc-button on:click={supprimer} label="supprimer" />