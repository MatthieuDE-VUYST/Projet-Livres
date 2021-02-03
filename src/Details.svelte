<script>
import { onMount } from 'svelte';
import PouchDB from 'pouchdb-browser';
import BoutonSuppr from "./Supprimer.svelte";


export let collection;
export let id_livre;
let db;

let etat = "details";
let  imgLivre, fileinput, imgName;

let title;
let _id;
let author;
let price;
let url;
let img;

onMount(
  async () => {
    const pdb = new PouchDB(collection);
    await pdb.info(); // Notre BD est disponible
    db = pdb; // db devient non nulle et donc déclenche load_books
    getLivre();

  }
);

const onFileSelected =(e)=>{
  let image = e.target.files[0];
          let reader = new FileReader();
          reader.readAsDataURL(image);
          reader.onload = e => {
                  img = e.target.result.replace('data:image/jpeg;base64,','')
                  imgName = image.name
          };
}

async function getLivre() {
  if (!db) return;

  db.get(id_livre).then(function(doc) {
    title = doc.title
    _id = doc._id
    author = doc.author
    price = doc.price
    url = doc.url
    img = doc.img.data
    return doc;
  }).then(function (result) {
    console.log(result);
  }).catch(function (err) { 
    console.log(err);
  });
}

async function modifLivre() {
  if (!db) return;
  db.get(_id).then(function(doc) {
      return db.put({
          _id: _id,
          _rev: doc._rev,
          author: document.getElementById("author").value,
          url: document.getElementById("url").value,
          price: document.getElementById("price").value,
          title: document.getElementById("title").value,
          img: {
              content_type: "image/jpeg",
              filename: imgName,
              data: img.replace('data:image/jpeg;base64,',''),
              path: "images/"+imgName
          }
      });
  }).then(function(response) {
      console.log(response);
      getLivre();
      afficheDetails();
  }).catch(function (err) {
      console.log(err);
  });
}

function afficheModif() {
  etat = "modif"
}

function afficheDetails() {
  etat = "details"
}

function afficheAll() {
  document.getElementById("home").click();
}
</script>

{#if etat == "details"}
  <main on:load={getLivre}>
    <h1>{title}</h1>
    <img src="data:image/jpeg;base64, {img}" alt="{title}" title="{title}"/>
    <p>Author: <b>{author}</b></p>
    <p>Price: <b>{price}€</b></p>
    <a href="{url}"><mwc-button label="Acheter"/></a>
    <BoutonSuppr collection="books" id_livre={_id}></BoutonSuppr>
    <mwc-button on:click={afficheModif} label="Modifier"/>
    <mwc-button on:click={afficheAll} label="Retour"/>
  </main>
{:else}
  <label for="title">Title</label>
  <input id="title" type="text" value="{title}" name="title"/>

  <label for="url">url</label>
  <input id="url" type="text" value="{url}" name="url"/>

  <label for="price">price</label>
  <input id="price" type="text" value="{price}" name="price"/>

  <label for="author">Author</label>
  <input id="author" type="text" value="{author}" name="author"/>

  {#if img}
  <img id="imageLivre" class="imgLivre" src="data:image/jpeg;base64, {img}" alt="d"/>
  {/if}
  <img class="upload" src="https://static.thenounproject.com/png/625182-200.png" alt="" on:click={()=>{fileinput.click();}} />
  <div class="chan" on:click={()=>{fileinput.click();}}>Choose Image</div>
  <input style="display:none" type="file" accept=".jpg, .jpeg, .png" on:change={(e)=>onFileSelected(e)} bind:this={fileinput} >

  <button on:click={modifLivre}>Modifier</button>
{/if}

<style>
mwc-button {
  --mdc-theme-primary: black;
  --mdc-theme-on-primary: orange;
}

img{
  height: 9em;
  width: auto;
}

.upload{
  display:flex;
  height:50px;
  width:50px;
  cursor:pointer;
}

.imgLivre{
  display: flex;
  height: 9em;
  width: auto;
}
</style>