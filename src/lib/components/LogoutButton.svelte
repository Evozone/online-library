<script>
    // Imports
    import { navigate } from "svelte-routing";
    import { auth } from "../../firebase";

    // Stores
    import { loggedInUser, showLoading } from "../../stores";

    // This function logs out the user
    const logout = async () => {
        $showLoading = true;
        try {
            await auth.signOut();
            await localStorage.removeItem("libraryToken");
            loggedInUser.set(null);
            navigate("/");
            $showLoading = false;
        } catch (error) {
            console.error("Error while logging out:", error);
        }
    };
</script>

<button on:click={logout} class="logout-button">
    <span>Logout</span>
</button>

<style>
    .logout-button {
        margin: 0;
        cursor: pointer;
        font-size: 0.9rem;
        font-weight: 500;
        padding: 0.5rem 1rem;
        position: relative;
        transition: all 0.2s ease-in-out;
    }
</style>
