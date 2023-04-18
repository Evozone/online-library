<script>
    // Imports
    import { onMount } from "svelte";
    import {
        doc,
        setDoc,
        Timestamp,
        collection,
        query,
        where,
        getDocs,
        getDoc,
        updateDoc,
    } from "firebase/firestore";
    import { navigate } from "svelte-routing";
    import { db } from "../../firebase";

    // Stores
    import { loggedInUser, showLoading } from "../../stores";

    import Navbar from "../components/Navbar.svelte";

    export let id;
    export let location;

    // Javascript
    let file;

    // Functions
    async function uploadBook() {
        showLoading.set(true);

        // Get the file
        const fileToUpload = file[0];

        // Get the file extension
        const fileExtension = fileToUpload.name.split(".").pop();

        // Get the file name
        const fileName = fileToUpload.name.split(".").slice(0, -1).join(".");

        // Get the file size
        const fileSize = fileToUpload.size;

        // Get the file type
        const fileType = fileToUpload.type;

        // Get the file last modified date
        const fileLastModifiedDate = fileToUpload.lastModifiedDate;

        // Upload the file
        // Post to firebase storage TODO
        console.log(fileToUpload);
        showLoading.set(false);
    }
</script>

<!-- Navbar -->
<Navbar />

<main>
    <!-- Page title -->
    <h1>Dashboard</h1>

    <!-- Dummy button to go to Book1, Book2, Book3 -->
    <button on:click={() => navigate("/book/1")}>Book 1</button>
    <button on:click={() => navigate("/book/2")}>Book 2</button>
    <button on:click={() => navigate("/book/3")}>Book 3</button>

    <!-- Upload PDF book -->
    <form on:submit|preventDefault={uploadBook}>
        <input type="file" bind:files={file} />
        <button type="submit">Upload</button>
    </form>
</main>

<style>
</style>
