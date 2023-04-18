<script>
    // Imports
    import { onMount } from "svelte";
    import { navigate } from "svelte-routing";
    import { loggedInUser } from "../../stores";

    // Components
    import GoogleLoginButton from "../components/GoogleLoginButton.svelte";

    // Exports
    export let location;

    onMount(() => {
        // If user is logged in, redirect to dashboard
        if ($loggedInUser !== null) {
            // If user is logged in and the path is a book, redirect to book
            if (location.pathname.includes("/book/")) {
                navigate(location.pathname);
            } else {
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
