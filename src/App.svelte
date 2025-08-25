<!-- 💡 JS  ═════════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 💡 JS -->
<script>

    // Imports:----------------------------------------------------------------
    import Button from "./Button.svelte";
    // Variables:--------------------------------------------------------------
    let currentPage = $state("accueil");
    let winnerName = $state(null);
    let winnerScore = $state(null);
    // Fonctions:--------------------------------------------------------------
    // Au démarrage de l'application: -----------------------------------------

</script>




<!-- 🔧 HTML  ═══════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 🔧 HTML -->
<main>
    {#if currentPage === "accueil"}
        <section id="accueil">
            <h1>MAXI-TREASURE</h1>
            <Button textButton="Créer joueur" />
            <Button textButton="Choisir joueur" />
            <div id="bloc-winner">
                <img src="../public/winner.png" alt="couronne du vainqueur">
                <p>record: <span class="gras">{winnerName}</span></p>
                <p>score: <span class="gras">{winnerScore}</span></p>
            </div>
        </section>
    {/if}
</main>




<!-- 🎨 CSS  ═════════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 🎨 CSS -->
<style>

    main {
        /* border: 3px solid red; */
        width: 100%;
        height: 100%;
    }

    section {
        /* border: 3px solid blue; */
        width: 100%;
        height: 100%;
        padding: 20px;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: flex-start;
        gap: 30px;
    }

    section h1 {
        font-size: 4rem;
        color: var(--secondary-color);
    }

    #bloc-winner {
        border: 1px solid pink;
        position: absolute;
        bottom: 20px;
        left: 20px;
        color: var(--secondary-color);
    }

    #bloc-winner img {
        width: 128px;
    }

    #bloc-winner p {
        font-size: 1.2rem;
        font-weight: 500;
    }

    .gras {
        font-size: 1.2rem;
        color: var(--tertiary-color);
        font-weight: 600;
    }

    /* Page d'accueil ----------------------------*/
    #accueil {
        position: relative;
        background-color: #000000;
        background-image: url(../public/treasure.jpg);
        background-position: bottom right;
        background-repeat: no-repeat;
        background-size: 60%;
    }

</style>
