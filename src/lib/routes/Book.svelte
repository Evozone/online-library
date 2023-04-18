<script>
    // Imports
    import { onMount } from "svelte";
    import {
        doc,
        getDoc,
        Timestamp,
        updateDoc,
        arrayUnion,
    } from "firebase/firestore";
    import { db } from "../../firebase";

    // Stores
    import { loggedInUser, showLoading } from "../../stores";

    // Components
    import Navbar from "../components/Navbar.svelte";

    // Exported variables
    export let location;
    export let id;
</script>

<Navbar />

<main>
    <!-- Page title -->
    <h1>Book {id}</h1>
</main>

<style>
    /* your styles go here */
</style>
