<script>
    // Imports
    import { navigate } from "svelte-routing";
    import { signInWithPopup, GoogleAuthProvider } from "firebase/auth";
    import { collection, addDoc } from "firebase/firestore";
    import { auth, db } from "../../firebase";
    import { loggedInUser, showLoading } from "../../stores";
    import jwt_decode from "jwt-decode";

    // JavaScript
    const provider = new GoogleAuthProvider();

    // This function logs in the user using the Google authentication provider
    const loginWithGoogle = async () => {
        // Show loading screen
        $showLoading = true;
        try {
            const result = await signInWithPopup(auth, provider);
            // @ts-ignore
            const user = result.user;

            // @ts-ignore
            const token = user.accessToken;
            const decodedUser = jwt_decode(token);

            window.localStorage.setItem("libraryToken", JSON.stringify(token));

            // Create user in database (see function below)
            await createUser(decodedUser);

            // Set the user in the store
            $loggedInUser = decodedUser;

            // Redirect to dashboard
            navigate("/dashboard");

            // Hide loading screen
            $showLoading = false;
        } catch (error) {
            console.error("Error while logging in with Google:", error);
        }
    };

    // This function creates a new user in the database
    const createUser = async (user) => {
        try {
            await addDoc(collection(db, "users"), {
                user_id: user.user_id,
                email: user.email,
                name: user.name,
                picture: user.picture,
                createdAt: new Date(),
            });
        } catch (error) {
            console.error("Error while creating user:", error);
        }
    };
</script>

<button on:click={loginWithGoogle} class="google-button"
    >Login with Google</button
>

<style>
    .google-button {
        /* background-color: #4285f4; */
        background-color: #669ffa;
        color: #fff;
        border: none;
        border-radius: 4px;
        padding: 10px 20px;
        font-size: 16px;
        font-weight: 500;
        cursor: pointer;
    }
</style>
