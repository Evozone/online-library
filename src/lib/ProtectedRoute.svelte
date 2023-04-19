<script>
    import { onMount } from "svelte";
    import { navigate } from "svelte-routing";
    import { loggedInUser } from "../stores";

    import Home from "./routes/Home.svelte";

    export let component;
    export let id;

    onMount(() => {
        // If user is not logged in, redirect to home page
        if (!$loggedInUser) {
            navigate("/");
        }
    });
</script>

{#if $loggedInUser}
    <svelte:component this={component} {id} location={null} />
{:else}
    <svelte:component this={Home} location="/" />
{/if}
