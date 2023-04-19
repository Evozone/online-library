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

<nav class="navbar" class:scrolled>
    <ul>
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
        <!-- Divider -->
        <div class="divider" />
        <!-- Brand clickable -->
        <li>
            <a href="/dashboard" class="text-white font-bold text-xl">
                <h3>Online Library</h3></a
            >
        </li>
    </ul>
    <!-- Logout Button -->
    <LogoutButton />
</nav>

<style>
    nav > ul {
        display: flex;
        align-items: center;
        list-style: none;
        padding: 0;
        margin: 0;
    }
    .navbar {
        box-sizing: border-box;
        display: flex;
        align-items: center;
        justify-content: space-between;
        background-color: black;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        transition: box-shadow 0.2s ease-in-out;

        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        padding: 0 1rem;
    }

    .divider {
        width: 1px;
        height: 2rem;
        background-color: white;
        margin: 0 1rem;
    }

    .avatar {
        width: 2.5rem;
        height: 2.5rem;
        border-radius: 50%;
        background-color: black;
        border: 2px solid white;
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
