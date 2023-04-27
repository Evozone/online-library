<script>
    // Imports
    import { onMount } from "svelte";
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
    <hgroup>
        <h1 class="hero-title">List of Books</h1>
        <!-- Upload book button -->
        <div class="upload-btn-container">
            <UploadBookBtn />
        </div>
    </hgroup>

    <!-- List of books in a card -->
    <article class="books">
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
    </article>
</main>

<style>
    .books {
        display: flex;
        flex-direction: column;
    }

    main {
        padding: 2rem;
        display: flex;
        flex-direction: column;
    }

    .upload-btn-container {
        display: flex;
        justify-content: flex-end;
    }
</style>
