<script>
    // Imports
    import { onMount } from "svelte";
    import { afterUpdate } from "svelte";
    import { db } from "../../firebase";
    import { collection, getDocs, query, where } from "firebase/firestore";

    // Stores
    import { showLoading } from "../../stores";

    // Components
    import Navbar from "../components/Navbar.svelte";
    import UploadBookBtn from "../components/UploadBookBtn.svelte";
    import BookCard from "../components/BookCard.svelte";

    export let id;
    export let location;

    let bookList = [];
    let searchedBooks = [];
    let searchTerms = "";
    let showSearch = false;

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

    const checkifEmpty = () => {
        if (searchTerms === "") {
            return true;
        }
    };

    const searchBook = async () => {
        searchedBooks = [];
        if (checkifEmpty()) {
            showSearch = false;
            return;
        }
        $showLoading = true;
        const q = query(
            collection(db, "books"),
            where("tags", "array-contains", searchTerms.toLowerCase())
        );
        try {
            const querySnapshot = await getDocs(q);
            if (querySnapshot.empty) {
                alert("No matching documents.");
            } else {
                querySnapshot.forEach((doc) => {
                    searchedBooks.push(doc.data());
                });
            }
        } catch (error) {
            alert("Error getting documents ");
            console.log(error);
        } finally {
            $showLoading = false;
            showSearch = true;
        }
    };

    onMount(async () => {
        await getBooks();
    });

    afterUpdate(() => {
        checkifEmpty();
    });
</script>

<!-- Navbar -->
<Navbar />

<main>
    <!-- Upload book button -->
    <div class="container-fluid">
        <h1 class="hero-title">List of Books</h1>
        <div class="search-bar">
            <input
                on:change={searchBook}
                class="search"
                type="search"
                id="search"
                name="search"
                placeholder="Search"
                bind:value={searchTerms}
            />
            <UploadBookBtn {getBooks} />
        </div>
    </div>

    {#if showSearch}
        <article class="books">
            <header>Search results</header>
            {#if searchedBooks.length === 0}
                <p>No books found</p>
            {:else}
                {#each searchedBooks as book}
                    <BookCard {book} />
                {/each}
            {/if}
        </article>
    {/if}

    <!-- List of books in a card -->
    {#if !showSearch}
        <article class="books">
            <header>All Books</header>
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
    {/if}
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

    .container-fluid {
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .search-bar {
        display: flex;
        align-items: center;
    }

    .search {
        background-color: white;
        padding: 0.5rem;
        height: 2rem;
        margin-bottom: 0;
        margin-right: 1rem;
    }

    article {
        margin-bottom: 0;
    }
</style>
