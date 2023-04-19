<script>
    // Imports
    import { onMount } from "svelte";
    import { navigate } from "svelte-routing";
    import { db } from "../../firebase";
    import { collection, getDocs, query } from "firebase/firestore";

    // Stores
    import { showLoading } from "../../stores";

    // Components
    import Navbar from "../components/Navbar.svelte";
    import UploadBookBtn from "../components/UploadBookBtn.svelte";
    import BookCard from "../components/BookCard.svelte";

    export let id;
    export let location;

    let bookList = [];

    // Fetch all books from Firestore
    const getBooks = async () => {
        $showLoading = true;
        try {
            const booksRef = await getDocs(query(collection(db, "books")));
            const data = booksRef.docs.map((doc) => doc.data());
            bookList = data;
            $showLoading = false;
        } catch (error) {
            console.log(error);
        }
    };

    onMount(async () => {
        await getBooks();
    });
</script>

<!-- Navbar -->
<Navbar />

<main>
    <!-- Page title -->
    <h1>Dashboard</h1>

    <!-- Upload book button -->
    <UploadBookBtn />

    <!-- List of books in a card -->
    <div class="grid books">
        {#await bookList}
            <p>Loading...</p>
        {:then books}
            {#if books.length === 0}
                <p>No books found</p>
            {:else}
                {#each books as book}
                    <BookCard {book} />
                {/each}
            {/if}
        {/await}
    </div>
</main>

<style>
    .books {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
        grid-gap: 1rem;
        margin: 1rem 0;
    }
</style>
