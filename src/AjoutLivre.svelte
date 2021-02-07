<script>
import { onMount } from 'svelte';
import PouchDB from 'pouchdb-browser';

export let collection;
let db;
let  avatar, fileinput, imgName;

onMount(
    async () => {
    const pdb = new PouchDB(collection);
    await pdb.info(); // Notre BD est disponible
    db = pdb; // db devient non nulle et donc déclenche load_books
    }
);

async function ajouterLivre() {
    if (!db) return;
    db.post({
        author: document.getElementById("author").value,
        url: document.getElementById("url").value,
        price: document.getElementById("price").value,
        title: document.getElementById("title").value,
        img: {
            content_type: "image/jpeg",
            filename: imgName,
            data: avatar.replace('data:image/jpeg;base64,',''),
            path: "images/"+imgName
        }
    }).then(function (response) {
        document.getElementById("home").click();
        console.log(response);
    }).catch(function (err) {
        console.log(err);
    });
}

const onFileSelected =(e)=>{
    let image = e.target.files[0];
            let reader = new FileReader();
            reader.readAsDataURL(image);
            reader.onload = e => {
                    avatar = e.target.result
                    imgName = image.name
            };
}
</script>

<label for="title">Title</label>
<input id="title" type="text" name="title"/>

<label for="url">url</label>
<input id="url" type="text" name="url"/>

<label for="price">price</label>
<input id="price" type="text" name="price"/>

<label for="author">Author</label>
<input id="author" type="text" name="author"/>

{#if avatar}
<img id="imageLivre" class="avatar" src="{avatar}" alt="d" />
{/if}
<img class="upload" src="https://static.thenounproject.com/png/625182-200.png" alt="" on:click={()=>{fileinput.click();}} />
<div class="chan" on:click={()=>{fileinput.click();}}>Choose Image</div>
<input style="display:none" type="file" accept=".jpg, .jpeg, .png" on:change={(e)=>onFileSelected(e)} bind:this={fileinput} >

<button on:click={ajouterLivre}>Ajouter</button>

<style>
.upload{
    display:flex;
height:50px;
    width:50px;
    cursor:pointer;
}
.avatar{
    display:flex;
    height:200px;
    width:200px;
}
</style>