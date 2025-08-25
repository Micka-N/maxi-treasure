<!-- 💡 JS  ═════════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 💡 JS -->
<script>

    // Imports:----------------------------------------------------------------
    import Button from "./Button.svelte";
    // Variables:--------------------------------------------------------------
    let currentPage = $state("accueil");    // Page courante
    let winnerName = $state(null);          // Nom recordman actuel
    let winnerScore = $state(null);         // Score recordman actuel
    let currentPlayer = $state({            // Objet joueur actuel
        nom: "",
        age: 0,
        ville: ""
    });

    // Fonctions:--------------------------------------------------------------
    // Au démarrage de l'application: -----------------------------------------

</script>




<!-- 🔧 HTML  ═══════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 🔧 HTML -->
<main>
    {#if currentPage === "accueil"}
        <section id="accueil">
            <h1>MAXI-TREASURE</h1>
            <Button textButton="Créer joueur" on:click={() => currentPage = "creation"}/>
            <Button textButton="Choisir joueur" />
            <div id="bloc-winner">
                <img src="../public/winner.png" alt="couronne du vainqueur">
                <p>record: <span class="gras">{winnerName}</span></p>
                <p>score: <span class="gras">{winnerScore}</span></p>
            </div>
        </section>
    {:else if currentPage === "creation"}
        <section id="creation">
            <h1>CREATION D'UN JOUEUR</h1>
            <form>
                <div id="contain-name">
                    <label for="name">Nom</label>
                    <input id="name" type="text" bind:value={currentPlayer.nom}>
                </div>
                <div id="contain-age">
                    <label for="age">Age</label>
                    <input id="age" type="text" bind:value={currentPlayer.age}>
                </div>
                <div id="contain-city">
                    <label for="city">Ville</label>
                    <input id="city" type="text" bind:value={currentPlayer.ville}>
                </div>
                <Button textButton="Jouer avec ce joueur"></Button>
            </form>
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
        background-color: #000000;
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
        font-size: clamp(3rem, 5vw, 6rem);
        color: var(--secondary-color);
    }

    #bloc-winner {
        /* border: 1px solid pink; */
        position: absolute;
        bottom: 20px;
        left: 20px;
        color: var(--secondary-color);
    }

    #bloc-winner img {
        width: 64px;
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
        background-image: url(../public/treasure.jpg);
        background-position: bottom right;
        background-repeat: no-repeat;
        background-size: 60%;
    }

    /* Page de création joueur --------------------*/
    #creation {
        position: relative;
        background-image: url(../public/treasure.jpg);
        background-position: bottom right;
        background-repeat: no-repeat;
        background-size: 60%;
    }

    form {
        /* border: 1px solid yellow; */
        width: 65%;
        max-width: 430px;
        min-width: 360px;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
        gap: 20px;
    }

    #creation div {
        /* border: 1px solid pink; */
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: clamp(1.3rem, 3vw, 2rem);
    }

    #creation label {
        color: var(--tertiary-color);
    }

    #creation input {
        width: 290px;
        height: fit-content;
        font-size: 1.5rem;
    }

    #contain-city {
        margin-bottom: 50px;
    }


</style>
