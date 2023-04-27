<script>
    // Imports
    import { navigate } from "svelte-routing";

    // Exports
    export let book;

    // Javascript
    let theme = localStorage.getItem("theme");
    // Switches the theme if the theme is dark
    if (theme === "dark") {
        theme = "light";
    } else {
        theme = "dark";
    }
</script>

<details>
    <!-- svelte-ignore a11y-no-redundant-roles -->
    <summary>
        <hgroup>
            <h2>{book.name}</h2>
            &nbsp; by &nbsp;
            <h5>{book.author}</h5>
        </hgroup>
    </summary>
    <article class="book-card">
        <p>{book.description}</p>
        <footer>
            <div>
                <span>Tags: </span>
                {#each book.tags as tag}
                    <kbd>{tag}</kbd>
                {/each}
            </div>
            <!-- Button to open link in new tab -->
            <button
                on:click={() => {
                    window.open(book.url, "_blank");
                }}
            >
                Read Online
            </button>
        </footer>
    </article>
</details>

<style>
    article {
        border: 1px solid var(--primary);
    }
    summary {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    hgroup {
        align-items: center;
        margin-bottom: 0;
    }

    hgroup h2,
    h5 {
        display: inline;
    }

    kbd {
        margin-right: 0.5rem;
    }

    footer {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    button {
        margin-bottom: 0;
        width: fit-content;
        font-size: 0.8rem;
        padding: 0.5rem;
    }
</style>
