<script>
    // Imports
    import { db, storage } from "../../firebase";
    import { ref, uploadBytes } from "firebase/storage";
    import { setDoc, doc } from "firebase/firestore";

    // Stores
    import { showLoading } from "../../stores";

    // Javascript
    let file;
    let bookDetails = {
        name: "",
        author: "",
        description: "",
        tags: [],
    };

    // Functions
    const uploadBook = async () => {
        // Show loading
        $showLoading = true;

        try {
            // Get the file
            const fileToUpload = file[0];

            let book = {
                fileExtension: fileToUpload.name.split(".").pop(),
                fileName: fileToUpload.name.split(".").slice(0, -1).join("."),
                fileSize: fileToUpload.size,
                fileType: fileToUpload.type,
                fileLastModifiedDate: fileToUpload.lastModifiedDate,
                file: fileToUpload,
            };

            // If the file is not a PDF, show alert and return
            if (book.fileExtension !== "pdf") {
                alert("Please upload a PDF file");
                return;
            }

            // Upload the file to Firebase Storage
            const storageRef = ref(
                storage,
                `books/${book.fileName}.${book.fileExtension}`
            );

            const uploadTask = await uploadBytes(storageRef, book.file).then(
                (snapshot) => {
                    const path = snapshot.metadata.fullPath;
                    const size = snapshot.metadata.size;
                    createBook(path, size);
                }
            );

            $showLoading = false;

            // Close the modal
            toggleModal({
                target: { dataset: { target: "upload-book-modal" } },
            });
        } catch (error) {
            console.error("Error while uploading book:", error);
        }
    };

    // This function creates a new book in the database
    const createBook = async (filePath, size) => {
        try {
            // Create the book in the database
            await setDoc(doc(db, "books", filePath.split("/")[1]), {
                createdAt: new Date(),
                name: bookDetails.name,
                author: bookDetails.author,
                description: bookDetails.description,
                tags: bookDetails.tags,
                filePath,
                size,
            });
        } catch (error) {
            console.error("Error while creating book:", error);
        }
    };

    // This function toggles the modal by adding and removing the "open" attribute
    const toggleModal = (e) => {
        const modal = document.getElementById(e.target.dataset.target);
        modal.toggleAttribute("open");
    };

    // Update live tags
    const updateLiveTags = (e) => {
        const tags = e.target.value.split(",");
        // Delete empty tags, trim spaces and remove the last tag
        bookDetails.tags = tags
            .filter((tag) => tag !== "" && tag !== " ")
            .map((tag) => tag.trim());
    };
</script>

<button data-target="upload-book-modal" on:click={toggleModal}>
    Upload a Book of your own!
</button>

<dialog id="upload-book-modal" class="upload-book-modal">
    <article class="modal-card">
        <header>
            <button
                aria-label="Close"
                class="close"
                data-target="upload-book-modal"
                on:click={toggleModal}
                on:keydown={toggleModal}>❌</button
            >
            <h2>Upload a Book</h2>
        </header>
        <form on:submit|preventDefault={uploadBook}>
            <div class="grid">
                <input
                    type="text"
                    bind:value={bookDetails.name}
                    placeholder="Book name"
                    required
                />
                <input
                    type="text"
                    bind:value={bookDetails.author}
                    placeholder="Author name"
                    required
                />
                <textarea
                    bind:value={bookDetails.description}
                    placeholder="Enter a description of the book"
                    required
                />
                <input
                    type="text"
                    on:input={updateLiveTags}
                    placeholder="Enter tags for the book"
                    required
                />
                <div class="tags">
                    <h3>Tags</h3>
                    {#if bookDetails.tags.length > 0}
                        {#each bookDetails.tags as tag}
                            <span>{tag}</span>
                        {/each}
                    {/if}
                </div>
                <input type="file" bind:files={file} required />
            </div>
            <button type="submit">Upload</button>
        </form>
    </article>
</dialog>

<style>
    .close {
        cursor: pointer;
        float: right;
        background-color: transparent;
        border: none;
    }

    .upload-book-modal {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        box-sizing: border-box;
        border: none;
        background-color: rgba(0, 0, 0, 0.5);
        z-index: 100;
    }

    .upload-book-modal[open] {
        display: grid;
        place-items: center;
    }

    .modal-card {
        width: fit-content;
        padding: 1rem;
        border-radius: 0.5rem;

        background-color: lightseagreen;
    }

    .grid {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 1rem;
    }

    .grid input,
    .grid textarea {
        padding: 0.5rem;
        border-radius: 0.5rem;
        border: none;
        background-color: white;
        color: #000;
    }

    .tags {
        grid-column: 1 / 3;
        display: flex;
        flex-wrap: wrap;
        gap: 0.5rem;
    }

    .tags h3 {
        margin-right: 0.5rem;
        margin-bottom: 0;
        margin-top: 0;
    }
    .tags span {
        padding: 0 0.5rem;
        border-radius: 0.5rem;
        background-color: white;
        color: #000;
    }

    .grid input[type="file"] {
        grid-column: 1 / 3;
    }
</style>
