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
    } from "firebase/firestore";
    import { navigate } from "svelte-routing";
    import { db } from "../../firebase";

    // Stores
    import { loggedInUser, showLoading } from "../../stores";

    import Navbar from "../components/Navbar.svelte";

    export let id;
</script>

<!-- Navbar -->
<Navbar />

<main>
    <!-- Page title -->
    <h1>Dashboard</h1>
</main>

<style>
</style>
