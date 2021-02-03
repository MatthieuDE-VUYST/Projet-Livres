<script>
import { onMount } from 'svelte';
import PouchDB from 'pouchdb-browser';
export let collection;
export let titre;
export let img;
export let auteur;
export let prix;
export let id_livre;
let db;

onMount(
  async () => {
    const pdb = new PouchDB(collection);
    await pdb.info(); // Notre BD est disponible
    db = pdb; // db devient non nulle et donc déclenche load_books
  }
);

async function getLivre() {
    if (!db) return;

    db.get(id_livre).then(function(doc) {
      return doc;
    }).then(function (result) {
      console.log(result);
    }).catch(function (err) { 
      console.log(err);
    });

  }

</script>

<body>
  <main on:load={getLivre}>
    <h1> {titre}!</h1>
    <p>{auteur}Welcome this is my <b>{prix}</b></p>
    <img src="data:image/jpeg;base64, {img}" alt="{titre}" title="{titre}"/>
  </main>
</body>

