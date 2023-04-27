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
        <img src="/logo.svg" alt="Main logo" width="400" />
        <div>
            <h1>Anil's Online Library</h1>
            <hgroup>
                <h3>
                    Online Library is a free and open source library for
                    everyone, you can read books online for free.
                </h3>
                <h5>Sign in to continue</h5>
            </hgroup>
            <GoogleLoginButton />
        </div>
    </div>
</main>

<style>
    main {
        height: 100vh;
        width: 100vw;
        margin: 0;
        padding: 5rem;
        background: linear-gradient(
            113deg,
            rgba(2, 0, 36, 0.5) 0%,
            rgba(9, 9, 121, 0.5) 35%,
            rgba(0, 212, 255, 0.5) 100%
        );
        color: aliceblue;
    }

    hgroup {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
    }
</style>
