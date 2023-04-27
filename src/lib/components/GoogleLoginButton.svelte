<script>
    // Imports
    import { navigate } from "svelte-routing";
    import { signInWithPopup, GoogleAuthProvider } from "firebase/auth";
    import { doc, setDoc, getDoc } from "firebase/firestore";
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
            // Check if user already exists
            const docRef = doc(db, "users", user.user_id);
            const docSnap = await getDoc(docRef);

            // If user does not exist, create it
            if (!docSnap.exists()) {
                await setDoc(doc(db, "users", user.user_id), {
                    user_id: user.user_id,
                    email: user.email,
                    name: user.name,
                    picture: user.picture,
                    createdAt: new Date(),
                    lastLoggedIn: new Date(),
                });
            } else {
                // If user already exists, update the lastLoggedIn field
                await setDoc(
                    doc(db, "users", user.user_id),
                    {
                        user_id: user.user_id,
                        email: user.email,
                        name: user.name,
                        picture: user.picture,
                        lastLoggedIn: new Date(),
                    },
                    { merge: true }
                );
            }
        } catch (error) {
            console.error("Error while creating user:", error);
        }
    };
</script>

<button on:click={loginWithGoogle} class="google-button">
    <!-- svelte-ignore a11y-missing-attribute -->
    <img src="src\assets\google.png" />
    Login with Google
</button>

<style>
    .google-button {
        width: fit-content;
        color: #fff;
        font-size: 16px;
        font-weight: 500;
    }
</style>
