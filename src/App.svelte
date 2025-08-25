<!-- 💡 JS  ═════════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 💡 JS -->
<script>

    // Imports:----------------------------------------------------------------
    import Button from "./Button.svelte";
    // Variables:--------------------------------------------------------------
    let currentPage = $state("accueil");
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
        </section>
    {/if}
</main>




<!-- 🎨 CSS  ═════════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 🎨 CSS -->
<style>

    main {
        border: 3px solid red;
        width: 100%;
        height: 100%;
    }

    section {
        border: 3px solid blue;
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
        font-size: 3.5rem;
        color: var(--secondary-color);
    }

    /* Page d'accueil ----------------------------*/
    #accueil {
        background-color: #000000;
        background-image: url(../public/treasure.jpg);
        background-position: bottom;
        background-repeat: no-repeat;
        background-size: 60%;
    }



</style>
