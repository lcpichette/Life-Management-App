<script lang='ts'>
    import { browser } from '$app/env';
    import Fab from '$lib/components/Fab.svelte';

    //
    /* SETTINGS */
    //

    let SYNC_MANAGER = {
        timer: 0,
        status: 'NOT_STARTED'
    };

    // Delay in seconds before a sync occurs after a user change.
    const SYNC_DELAY = 5;

    //
    /* GLOBAL VARIABLES */
    //

    let blockType = 'text';
    let blockURL = '';

    //
    /* HELPER FUNCTIONS */
    //

    function seconds(amount: number) {
        return 1000*amount;
    }

    function urlCleanser(url: string): string {
        if (!(url.includes('http://') || url.includes('https://'))) {
            return 'https://' + url;
        } 
        return url;
    }

    //
    /* CORE FUNCTIONS */
    //

    function blockImageURLUpdated() {
        if (browser) {
            blockURL = document.getElementById('newBlockModal-ImageURL')?.value;
        }
    }

    function syncCurrentPage() {

    }

    //! Replace enter-to-create-block with: 
    //! Click the + button to create a new block below the one you're currently in.
    function createBlock(): HTMLDivElement | undefined {
        let newBlock: HTMLDivElement | undefined = undefined;
        const type = blockType; //! TODO: Determine type from modal

        if (type === 'paragraph') {
            newBlock = document.createElement('div');
            newBlock.contentEditable = 'true';
            newBlock.classList.add('BLOCK', 'PARAGRAPH');
        } else if (type === 'image') {
            const url = urlCleanser(blockURL); //! TODO: Determine type from modal
            newBlock = document.createElement('div');
            newBlock.classList.add('BLOCK', 'IMAGE');
            newBlock.style.backgroundImage = `url(${url})`;
        }
        
        return newBlock;
    }

    function createNewBlock() {
        if (browser) {
            const block = createBlock();
            if (block) {
                document.querySelector('#note-region')?.appendChild(block);
            }
        }
    }

    if (browser) {
        document.addEventListener('onkeyup', () => {
            if (SYNC_MANAGER.timer && SYNC_MANAGER.status === 'COMPLETE') {
                window.clearTimeout(SYNC_MANAGER.timer);
            } else if (!SYNC_MANAGER.timer) {
                SYNC_MANAGER.timer = window.setTimeout(syncCurrentPage, seconds(SYNC_DELAY));
            }
        });
    }

    function openModal() {
        if (browser) {
            const openModalButton = document.getElementById('createBlockButton');
            const modal = document.getElementById('newBlockModal');
            const modalBackdrop = document.getElementById('newBlockModal-backdrop');
            if (openModalButton) {
                openModalButton.disabled = true;
            }
            if (modal) {
                modal.style.display = 'flex';
            }
            if (modalBackdrop) {
                modalBackdrop.style.display = 'block';
            }
        }
    }

    function closeModal() {
        if (browser) {
            const openModalButton = document.getElementById('createBlockButton');
            const modal = document.getElementById('newBlockModal');
            const modalBackdrop = document.getElementById('newBlockModal-backdrop');
            if (openModalButton) {
                openModalButton.disabled = false;
            }
            if (modal) {
                modal.style.display = 'none';
            }
            if (modalBackdrop) {
                modalBackdrop.style.display = 'none';
            }
        }
    }

    function submitModal() {
        if (browser) {
            createNewBlock();

            closeModal();
        }
    }

    function blockTypeUpdated() {
        blockType = document.getElementById('newBlockModal-type')?.value;

        const urlInput = document.getElementById('newBlockModal-ImageURL-wrapper');
        if (blockType === 'image') {
            // URL Input for images
            if (urlInput) {
                urlInput.style.display = 'block';
            }
        } else {
            // URL Input for images
            const urlInput = document.getElementById('newBlockModal-ImageURL-wrapper');
            if (urlInput) {
                urlInput.style.display = 'none';
            }
        }
    }
</script>

<main>
    <section id='note-region'>
        <div id='title' class='BLOCK PARAGRAPH' contenteditable="true"></div>
    </section>
    <Fab onClick={openModal}></Fab>
    
    <div id='newBlockModal-backdrop' on:click={closeModal}></div>
    <div id='newBlockModal'>
        <div class='modalOptions'>
            <span>New Note</span><br>
            <select id='newBlockModal-type' type='dropdown' on:change={blockTypeUpdated}>
                <option value='paragraph'>Paragraph</option>
                <option value='image'>Image</option>
            </select>
            
            <div id='newBlockModal-ImageURL-wrapper'>
                <label for='newBlockModal-ImageURL'>Image URL:</label>
                <!--TODO Change this to upload, and then return link to S3 bucket -->
                <input id='newBlockModal-ImageURL' type='text' placeholder='e.g. http://somefantasticimage.com...' on:change={blockImageURLUpdated}/>
            </div>
        </div>
        
        <div class='modalButtons'>
            <button on:click={closeModal}>Cancel</button>
            <button on:click={submitModal}>Submit</button>
        </div>
    </div>
</main>

<style lang='scss'>
    :global(.BLOCK) {
        border-radius: 4px;
        outline: none;
    }
    :global(.PARAGRAPH) {
        border: 1px solid rgba(0,0,0,.2);
        border-radius: 4px;
        outline: none;
    }
    :global(.IMAGE) {
        border-radius: 4px;
        outline: none;

        width: 100%;
        height: 0;
        background-size: contain;
        background-repeat: no-repeat;
        padding-top: 66.64%;
    }

    #newBlockModal {
        display: none;

        justify-content: space-between;
        flex-direction: column;

        border-radius: 8px;
        background-color: white;
        padding: 1.5rem;
        width: 400px;
        height: 500px;

        position: absolute;
        z-index: 10000;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);

        #newBlockModal-ImageURL-wrapper {
            display: none;
        }
        .modalButtons {
            display: flex;
            bottom: 0;
        }
    }

    #newBlockModal-backdrop {
        display: none;

        background-color: rgba(0,0,0,0.5);
        filter: blur(4px);
        
        width: 100vw;
        height: 100vh;

        position: absolute;
        top: 0;
        left: 0;
    }
</style>