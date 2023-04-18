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
        background-color: #f44336;
        border-radius: 10px;
        border: none;
        color: white;
        padding: 10px 20px;
        text-align: center;
        text-decoration: none;
        font-size: 20px;
        cursor: pointer;
    }

    .logout-button:hover {
        background-color: #fa695f;
    }
</style>
