<script>
    // Imports
    import { db, storage } from "../../firebase";
    import { ref, uploadBytes, getDownloadURL } from "firebase/storage";
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
                async (snapshot) => {
                    const path = snapshot.metadata.fullPath;
                    const size = snapshot.metadata.size;
                    const url = await getDownloadURL(snapshot.ref);
                    createBook(path, size, url);
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
    const createBook = async (filePath, size, url) => {
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
                url,
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

<button
    class="upload-book-btn contrast"
    data-target="upload-book-modal"
    on:click={toggleModal}
>
    Upload a Book of your own!
</button>

<dialog id="upload-book-modal">
    <article>
        <header>
            <!-- svelte-ignore a11y-missing-content -->
            <a
                class="close"
                href="#close"
                data-target="upload-book-modal"
                on:click={toggleModal}
            />
            Upload a Book
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
            </div>
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
                <h6>Tags</h6>
                {#if bookDetails.tags.length > 0}
                    {#each bookDetails.tags as tag}
                        <kbd>{tag}</kbd>
                        &nbsp;
                    {/each}
                {/if}
            </div>
            <label for="file"
                >Choose the book PDF
                <input
                    name="file"
                    id="file"
                    type="file"
                    bind:files={file}
                    required
                />
            </label>
            <input type="submit" value="Upload" />
        </form>
    </article>
</dialog>

<style>
    .upload-book-btn {
        width: fit-content;
        padding: 0.5rem 1rem;
        border-radius: 0.5rem;
        border: none;
        font-size: 0.75rem;
        font-weight: 600;
        margin-bottom: 0;
    }

    .tags {
        margin-bottom: 1rem;
    }
</style>
