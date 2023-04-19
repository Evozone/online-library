<script>
    // Imports
    import { navigate } from "svelte-routing";

    // Exports
    export let book;
</script>

<article
    class="book-card"
    on:click={() => {
        navigate(`/book/${book.filePath.split("/")[1].split(".")[0]}`);
    }}
    on:keydown={(e) => {
        if (e.key === "Enter") {
            navigate(`/book/${book.filePath.split("/")[1].split(".")[0]}`);
        }
    }}
>
    <div class="headings">
        <h2>Name: {book.name}</h2>
        <h3>Author: {book.author}</h3>
        <h6>
            Size: {#if book.size > 1000}
                {(book.size / 1000000).toFixed(2)} MB
            {/if}
            {#if book.size < 1000}
                {book.size} KB
            {/if}
        </h6>
    </div>
    <div class="description">
        <p>{book.description}</p>
    </div>
    <div>
        <span>Tags: </span>
        {#each book.tags as tag}
            <span class="tag">{tag}</span>
        {/each}
    </div>
</article>

<style>
    .book-card {
        background-color: lightsteelblue;
        border-radius: 5px;
        padding: 1rem;
    }

    .book-card:hover {
        cursor: pointer;
        background-color: lightblue;
    }
</style>
