<script>
    // Imports
    import { onMount } from "svelte";
    import { navigate } from "svelte-routing";
    import { loggedInUser } from "../../stores";

    // Components
    import GoogleLoginButton from "../components/GoogleLoginButton.svelte";

    // Exports
    export let location;

    onMount(async () => {
        // If user is logged in, wait for path to change
        if ($loggedInUser) {
            const path = window.location.pathname;
            if (path === "/") {
                navigate("/dashboard");
            }
        }
    });
</script>

<main>
    <div class="grid">
        <h1>Welcome to Online Library</h1>
        <p>
            This is a simple Svelte app that uses Firebase for authentication
            and database.
        </p>
        <!-- This button is important -->
        <GoogleLoginButton />
        <!-- This button is important -->
    </div>
</main>

<style>
    main {
        height: 100vh;
        width: 100vw;
        margin: 0;
        padding: 0;
        background-color: #1a202c;
        background-repeat: repeat;
        background-size: cover;

        display: grid;
        place-items: center;
    }
</style>
