<script>
    import { Router, Route } from "svelte-routing";
    import { onMount } from "svelte";
    import ProtectedRoute from "./lib/ProtectedRoute.svelte";
    // Routes
    import Book from "./lib/routes/Book.svelte";
    import Dashboard from "./lib/routes/Dashboard.svelte";
    import Home from "./lib/routes/Home.svelte";

    // Stores
    import { loggedInUser, showLoading } from "./stores";

    // Components
    import Loading from "./lib/components/Loading.svelte";

    onMount(() => {
        // Check if user is logged in
        // Check if token is present in local storage
        const token = window.localStorage.getItem("libraryToken") || null;
        if (token) {
            loggedInUser.login(token);
        }

        // Don't redirect to any page outside of /, /dashboard, /book/:id
        const path = window.location.pathname;
        if (
            path !== "/" &&
            path !== "/dashboard" &&
            !path.startsWith("/book/")
        ) {
            console.log("Redirecting to /");
            window.location.pathname = "/";
        }
    });
</script>

{#if $showLoading}
    <Loading />
{/if}

<Router>
    <Route path="/">
        <Home location="/" />
    </Route>
    <Route path="/dashboard">
        <ProtectedRoute component={Dashboard} id={null} />
    </Route>
    <Route path="/book/:id" let:params>
        <ProtectedRoute component={Book} id={params.id} />
    </Route>
</Router>

<style>
    :global(body) {
        margin: 0;
        padding: 0;
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto",
            "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans",
            "Helvetica Neue", sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
    }

    :global(code) {
        font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
            monospace;
    }
</style>
