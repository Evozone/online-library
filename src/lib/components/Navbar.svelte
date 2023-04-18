<script>
    import { onMount } from "svelte";

    // Components
    import LogoutButton from "../components/LogoutButton.svelte";

    // Stores
    import { loggedInUser } from "../../stores";

    // Params
    export let bookName = null;

    // Javascript variables
    let scrolled = false;

    // On mount check if the page has an offset
    // If the page has an offset, then add the scrolled class to the navbar
    onMount(() => {
        if (window.pageYOffset > 0) {
            scrolled = true;
        }

        window.addEventListener("scroll", () => {
            if (window.pageYOffset > 0) {
                scrolled = true;
            } else {
                scrolled = false;
            }
        });
    });
</script>

<div class="navbar" class:scrolled>
    <div class="avatar">
        <!-- If loggedInUser is present and the photoUrl is present, then show img else show placeholder -->
        {#if $loggedInUser && $loggedInUser.picture}
            <img src={$loggedInUser.picture} alt={$loggedInUser.name} />
        {:else}
            <span class="text-xl font-bold">
                {#if $loggedInUser}
                    {$loggedInUser.name.charAt(0)}
                {/if}
            </span>
        {/if}
    </div>
    <!-- Brand -->
    <h1 class="text-2xl font-bold text-content-base">WebMarks</h1>

    <!-- Logout Button -->
    <LogoutButton />
</div>

<style>
    .navbar {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 1rem 2rem;
        background-color: black;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        transition: box-shadow 0.2s ease-in-out;
    }

    .avatar {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 3rem;
        height: 3rem;
        border-radius: 50%;
        background-color: black;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    .avatar img {
        width: 100%;
        height: 100%;
        border-radius: 50%;
    }

    .scrolled {
        background-color: black;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }
</style>
