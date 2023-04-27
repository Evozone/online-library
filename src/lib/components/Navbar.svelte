<script>
    // Svelte imports
    import { onMount } from "svelte";
    import { navigate } from "svelte-routing";
    import { themeChange } from "theme-change";

    // Components
    import LogoutButton from "../components/LogoutButton.svelte";

    // Stores
    import { loggedInUser } from "../../stores";

    // Params
    export let bookName = null;

    // Javascript variables
    let scrolled = false;

    // Functions
    const navigateToHome = () => {
        navigate("/dashboard");
    };

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

        themeChange(false);
    });
</script>

<nav class:scrolled>
    <ul>
        <li class="avatar">
            <!-- If loggedInUser is present and the photoUrl is present, then show img else show placeholder -->
            {#if $loggedInUser && $loggedInUser.picture}
                <img src={$loggedInUser.picture} alt={$loggedInUser.name} />
            {:else}
                <span>
                    {#if $loggedInUser}
                        {$loggedInUser.name.charAt(0)}
                    {/if}
                </span>
            {/if}
        </li>
    </ul>
    <ul>
        <li>
            <strong> Online Library </strong>
        </li>
    </ul>
    <ul>
        <ul>
            <input
                type="checkbox"
                name="theme"
                role="switch"
                data-toggle-theme="dark,light"
                data-act-class="ACTIVECLASS"
            />
        </ul>
        <ul>
            <LogoutButton />
        </ul>
    </ul>
</nav>

<style>
    nav {
        padding: 0 1.5rem;
        background-color: rgba(0, 0, 0, 0.5);
        transition: all 0.5s ease-in-out;
        position: sticky;
        top: 0;
        z-index: 100;
    }

    .avatar {
        display: flex;
        align-items: center;
        justify-content: center;
        padding: 0;
        height: 2.5rem;
        width: 2.5rem;
        border-radius: 50%;
        border: 1px solid #fff;
        margin-right: 1rem;
    }

    .avatar img {
        height: 100%;
        width: 100%;
        border-radius: 50%;
    }

    .scrolled {
        background-color: rgba(0, 0, 0, 0.8);
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
    }

    strong {
        color: var(--primary);
    }
</style>
