<script>
    // Imports
    import { onMount } from "svelte";
    import { doc, getDoc } from "firebase/firestore";
    import { db } from "../../firebase";

    // Stores
    import { showLoading } from "../../stores";

    // Components
    import Navbar from "../components/Navbar.svelte";

    // Exported variables
    export let location;
    export let id;

    // Javascript variables
    let bookinfo = {};

    // Fetch book info from Firestore
    const getBook = async () => {
        $showLoading = true;
        try {
            const bookRef = doc(db, "books", id + ".pdf");
            const bookDoc = await getDoc(bookRef);
            if (bookDoc.exists()) {
                bookinfo = bookDoc.data();
                $showLoading = false;
            } else {
                console.log("No such document!");
            }
        } catch (error) {
            console.log(error);
        }
    };

    onMount(async () => {
        await getBook();
    });
</script>

<Navbar bookName={bookinfo.name} />

<main>
    <!-- Properties: name, author, description, tags, size -->
    <h1>{bookinfo.name}</h1>
    <p>{bookinfo.author}</p>
    <p>{bookinfo.description}</p>
    <p>{bookinfo.tags}</p>
    <p>{bookinfo.size}</p>

    <!-- Button to open link in new tab -->
    <button
        on:click={() => {
            window.open(bookinfo.url, "_blank");
        }}
    >
        Open in new tab
    </button>
</main>

<style>
    main {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100%;
    }
</style>
