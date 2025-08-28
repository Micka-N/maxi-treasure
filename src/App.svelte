<!-- 💡 JS  ═════════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 💡 JS -->
<script>

    // Imports:----------------------------------------------------------------
    import Button from "./Button.svelte";               // Import pour Button
    import { onMount } from "svelte";                   // Import pour onMount



    // Variables:--------------------------------------------------------------
    let currentPage = $state("accueil");                // Page courante
    let listPlayersLocal = $state([]);                  // Tous les joueurs crées (tableau d'objets local chargé onMount)
    let fullListItems = $state([]);                     // Tous les objets (tableau d'objets local chargé onMount)
    let itemsFoundsInBag = $state([]);                  // Tous les objets trouvés (tab. d'objets local)
    let roundSearch = $state(0);                        // Round de recherche sur 10
    let sixSlots = $state([0, 1, 2, 3, 4, 5]);          // Tableau de 6 (pour boucler 6 fois avec boucle each)
    let finish = $state(false);                         // État pour afficher les messages de fin de game
    let winnerPlayer = $state({                         // Meilleur joueur (objet)
        nom: null,
        age: null,
        ville: null,
        score: null, 
        is_a_best: true
    });
    let currentPlayer = $state({                        // Joueur actuel (objet)
        nom: "",
        age: null,
        ville: "",
        score: null,
        is_a_best: false
    });
    let lastItemPick = $state({                         // Le dernier objet pioché (obj)
        nom: "",
        type: "",
        points: null,
        rarete: "",
        id: null
    });



    // Fonctions:--------------------------------------------------------------
    async function saveNewPlayer() {                    // Fonction pour ajout joueur POST (PB) ⬇️ 
        const newPlayer = {
            nom: currentPlayer.nom,
            age: currentPlayer.age,
            ville: currentPlayer.ville
        };
        await fetch("http://127.0.0.1:8090/api/collections/joueurs/records", {
            method: "POST",
            headers: {"Content-type":"application/json"},
            body: JSON.stringify(newPlayer)
        });
        currentPlayer.nom = "";       // On vide les champs:
        currentPlayer.age = null;
        currentPlayer.ville = "";
    }
    async function saveBestPlayer() {
        // await fetch("http://127.0.0.1:8090/api/collections/joueurs/records", {
        //     method: "PATCH",
        //     headers: {"Content-type":"application/json"},
        //     body: JSON.stringify(bestPlayer)
        // })
    }
    function addPlayer() {                              // Fonction pour ajout joueur ---- ⬆️ 
        listPlayersLocal.push({ ...currentPlayer });    // Ajout du joueur crée/courant au tableau jpueurs (LOCAL)
        saveNewPlayer();                                // Ajout du joueur crée/courant dans liste joueurs (PB)
        // console.log("Liste joueurs:", listPlayersLocal.map(p => ({ ...p })));   // Pour logguer proprement avec $state
    }
    function choosePlayer(joueur) {                     // Fonction pour définir joueur en cours après choix
        currentPlayer = joueur;
    }
    function pickItemRandom() {                         // Fonction pour piocher un objet 
        let nombreAuHasard = Math.floor(Math.random() * (fullListItems.length)); //TODO Formule à vérifier
        lastItemPick = fullListItems[nombreAuHasard];   // Un objet au hasard dans la variable lastItemPick
        let idItemPick = lastItemPick.id;               // L'id de l'objet au hasard
        fullListItems = fullListItems.filter(obj => obj.id !== idItemPick);  // On garde que ceux qui n'ont pas l'Id trouvé
        roundSearch ++;
    }
    async function deletePlayer(id) {                   // Fonction pour supprimer un joueur
        await fetch(`http://127.0.0.1:8090/api/collections/joueurs/records/${id}`, {
            method: "DELETE"
        });

        listPlayersLocal = listPlayersLocal.filter(perso => perso.id !== id);   // Suppression locale
    }
    function addItemInBag() {                           // Ajout du dernier objet pioché à l'inventaire
        itemsFoundsInBag.push(lastItemPick);            // Le dernier objet trouvé dans le tableau itemsFoundsInBag
        currentPlayer.score += lastItemPick.points;     // Ajout des points du dernier objet au total
        lastItemPick = {                                // Réinitialisation de lastItemPick
        nom: "",
        type: "",
        points: null,
        rarete: "",
        id: null
    };
     if (roundSearch === 10 || itemsFoundsInBag.length === 6) {   // Activer état "fin de game" si 10 fouilles ou sac plein
            finish = true;
            console.log(fullListItems);
        }
    }
    function deleteItem() {                             // Remise à zéro du dernier item pioché après "jeter"
        lastItemPick = {                                // Réinitialisation de lastItemPick
        nom: "",
        type: "",
        points: null,
        rarete: "",
        id: null
    };
    if (roundSearch === 10 || itemsFoundsInBag.length === 6) {   // Activer état "fin de game" si 10 fouilles ou inventaire plein
            finish = true;
        }
    }
    function resetEndGame() {                           // Reset apres fin de partie
        if (currentPlayer.score > winnerPlayer.score) { // Si meilleur joueur, remplace le précédent
            currentPlayer.is_a_best = true;
            winnerPlayer = currentPlayer;
        }
        currentPlayer = null;                           // Tous les resets ⤵️
        lastItemPick = {
        nom: "",
        type: "",
        points: null,
        rarete: "",
        id: null
    };
    itemsFoundsInBag = [];
    roundSearch = 0;
    finish = false;
    getPBItems();
    }


    // Au démarrage de l'application: -----------------------------------------
    onMount(async() => {                                // Chargement joueurs et items existants sur PB
        getPBPlayers();                                 // On charge la liste des joueurs déjà crées au démarrage
        getPBItems();                                   // On charge la liste des objets trouvables au démarrage
    })
    async function getPBItems() {
        const responce2 = await fetch("http://127.0.0.1:8090/api/collections/objets/records");
        const data2 = await responce2.json();
        fullListItems = [...data2.items];               // On charge la liste des objets trouvables au démarrage
        console.log(fullListItems);
    }
    async function getPBPlayers() {
        const response = await fetch("http://127.0.0.1:8090/api/collections/joueurs/records");
        const data = await response.json();
        listPlayersLocal = [...data.items];             // On charge la liste des joueurs déjà crées au démarrage
    }

</script>




<!-- 🔧 HTML  ═══════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 🔧 HTML -->
<main>
    <!-- ---------------------------- Page d'ACCUEIL :-------------------------- -->
    {#if currentPage === "accueil"}
        <section id="accueil">
            <h1>MAXI-TREASURE</h1>
            <Button textButton="Choisir un joueur" on:click={() => currentPage = "choix"}/>
            <p id="para">Partez à la conqûete du plus gros trésor !<br>
                Gardez les objets ayant le plus de valeur...
            </p>
            <div id="bloc-winner">
                <img src="../public/winner.png" alt="couronne du vainqueur">
                <p>record: <span class="gras">{winnerPlayer.nom}</span></p>
                <p>score: <span class="gras">{winnerPlayer.score}</span></p>
            </div>
        </section>
    <!-- --------------------------- Page de CRÉATION :------------------------- -->
    {:else if currentPage === "creation"}
        <section id="creation">
            <h1>CREATION D'UN JOUEUR</h1>
            <div id="contain-form">
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
                <Button textButton="Créer ce joueur" on:click={() => {addPlayer; currentPage = "choix"}} ></Button>
            </div>
            <Button textButton="Voir les joueurs" on:click={() => currentPage = "choix"}></Button>
            <Button textButton="Revenir à l'accueil" on:click={() => currentPage = "accueil"}></Button>
        </section>
    <!-- ----------------------------- Page de CHOIX :-------------------------- -->
    {:else if currentPage === "choix"}
        <section id="choix">
            <h1>CHOISIR UN JOUEUR EXISTANT</h1>
            <ul>
                {#each listPlayersLocal as joueur}
                    <li>
                        <div class="info">
                            <span class="gras space">{joueur.nom}</span>
                            <span>{joueur.age} ans de {joueur.ville}</span>
                        </div>
                        <div class="actions">
                            <Button textButton="Jouer avec ce joueur" on:click={() => {choosePlayer(joueur); currentPage = "map"}} />
                            <Button textButton="❌" on:click={() => deletePlayer(joueur.id)} />
                        </div>
                    </li>
                {/each}
            </ul>
            <Button textButton="Créer un joueur" on:click={() => currentPage = "creation"}/>
            <Button textButton="Revenir à l'accueil" on:click={() => currentPage = "accueil"}></Button>
        </section>
    <!-- --------------------------- Page de jeu: MAP :------------------------- -->
    {:else if currentPage === "map"}
        <section id="map">
            <article id="ground">
                {#if finish === false}
                    <div id="contain-riv"><Button textButton="Chercher dans la rivière..." on:click={pickItemRandom}></Button></div>
                    <div id="contain-for"><Button textButton="Chercher dans la forêt..." on:click={pickItemRandom}></Button></div>
                    <div id="contain-mon"><Button textButton="Chercher dans le montagne..." on:click={pickItemRandom}></Button></div>
                    <div id="contain-mar"><Button textButton="Chercher dans le marais..." on:click={pickItemRandom}></Button></div>
                    <div id="contain-des"><Button textButton="Chercher dans le désert..." on:click={pickItemRandom}></Button></div>
                    <div id="contain-mer"><Button textButton="Chercher dans la mer..." on:click={pickItemRandom}></Button></div>
                {:else}
                    <div id="contain-riv"></div>
                    <div id="contain-for"></div>
                    <div id="contain-mon"></div>
                    <div id="contain-mar"></div>
                    <div id="contain-des"></div>
                    <div id="contain-mer"></div>
                {/if}
            </article>
            <!-- -------------------- Partie INVENTAIRE -------------------- -->
            {#if roundSearch !== 0}
                <article id="stuff">
                    <div id="contain-bloc1">
                        <p>Fouille(s) effectuée(s) <span id="compteur-fouille">{roundSearch}</span>/10</p>
                        {#if finish}
                        <p>Session de fouilles terminée !</p>
                        {/if}
                        <div class="trait"></div>
                        {#if finish}
                            <p>🪏</p>
                        {:else}
                        <div id="contain-objet-trouve">
                            {#if lastItemPick.nom !== ""}
                            <p>Objet trouvé:</p>
                            <span id="objet">{`${lastItemPick.nom}`}</span>
                            <div id="contain-caracteristiques">
                                <span>{lastItemPick.type}</span>
                                <span>{lastItemPick.rarete}</span>
                            </div>
                            <div id="contain-points">
                                <p>valeur:</p>
                                <span class="purple-big">{lastItemPick.points}</span>
                            </div>
                            <div class="contain-btns-bloc1">
                                <Button textButton="Garder" on:click={addItemInBag}></Button>
                                <Button textButton="Jeter" on:click={deleteItem}></Button>
                            </div>
                            {/if}
                        </div>
                        {/if}
                    </div>
                    {#if itemsFoundsInBag.length >= 1}
                        <div id="contain-inventaire">
                            {#if finish}
                                <p>Inventaire au complet</p>
                            {:else}
                                <p>Mon inventaire</p>
                            {/if}
                            {#each sixSlots as i}
                                <div class="slot">
                                    <div class="number-order">
                                        <p>{i + 1}</p>
                                    </div>
                                        {#if itemsFoundsInBag[i]}
                                            <div class="item-in-slot full">
                                                <span>{itemsFoundsInBag[i].nom}</span>
                                                <span class="purple">{itemsFoundsInBag[i].points}</span>
                                            </div>
                                        {:else}
                                            <div class="item-in-slot">
                                                <p class="vide">empty...</p>
                                            </div>
                                        {/if}
                                </div>
                            {/each}
                        </div>
                        {#if finish}
                        <div id="contain-score-game-finish">
                            <p>Total de points pour {currentPlayer.nom}: 
                                <span class="purple-big">{currentPlayer.score}</span>
                            </p>
                            <Button textButton="Rejouer" on:click={() => {resetEndGame(); currentPage = "choix"}}></Button>
                            <Button textButton="Accueil" on:click={() => {resetEndGame(); currentPage = "accueil"}}></Button>
                        </div>
                        {:else}
                            <div id="contain-score-game">
                                <p>Total de points pour {currentPlayer.nom}: 
                                    <span class="purple-big">{currentPlayer.score}</span>
                                </p>
                            </div>
                        {/if}
                    {/if}
                </article>
            {/if}
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
        color: #EFD19A;
    }

    .gras {
        font-size: 1.4rem;
        color: #664C35;
        font-weight: 900;
    }

    .trait {
        width: 100%;
        height: 1px;
        background-color: var(--primary-color);
        margin-bottom: 20px;
        margin-top: 5px;
    }

    .vide {
        color: var(--fifth-color);
        padding-top: 3px;
    }



    /* ---------------------------- Page d'accueil ----------------------------*/
    #accueil {
        position: relative;
        background-image: url(../public/treasure.jpg);
        background-position: bottom right;
        background-repeat: no-repeat;
        background-size: 60%;
    }

    #para {
        color: #EFD19A;
        margin-top: 25px;
    }

    /* ------------------------ Page de création joueur -----------------------*/
    #creation {
        position: relative;
        background-image: url(../public/treasure.jpg);
        background-position: bottom right;
        background-repeat: no-repeat;
        background-size: 60%;
    }

    #contain-form {
        /* border: 1px solid yellow; */
        width: 65%;
        max-width: 430px;
        min-width: 360px;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
        gap: 20px;
        margin-bottom: 125px;
    }

    #creation div {
        /* border: 1px solid pink; */
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: clamp(1.2rem, 3vw, 1.7rem);
    }

    #creation label {
        color: var(--tertiary-color);
    }

    #creation input {
        width: 290px;
        height: fit-content;
        font-size: 1.2rem;
    }

    #contain-city {
        margin-bottom: 50px;
    }


    /* ----------------------- Page de choix joueur ---------------------------*/
    #choix {
        position: relative;
        background-image: url(../public/treasure.jpg);
        background-position: bottom right;
        background-repeat: no-repeat;
        background-size: 60%;
    }

    #choix ul {
        width: 70%;
        max-width: 550px;
        min-width: fit-content;
        color: var(--fourth-color);
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: flex-start;
        gap: 10px;
        margin-bottom: 30px;
    }

    #choix li {
        background-color: var(--fifth-color);
        color: black;
        width: 100%;
        border-radius: 10px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        gap: 10px;
        padding: 0 10px;

    }

    #choix li .info {
        display: flex;
        flex-direction: column;
        flex: 1;
        color: #7E260C;
        font-size: clamp(0.7rem, 2.4vw, 1rem);
        text-wrap: nowrap;
    }

    #choix li .info span:nth-child(2) {
        padding-bottom: 4px;
    }

    #choix li .actions {
        display: flex;
        gap: 10px;
    }



    /* ---------------------------- Page de map -------------------------------*/
    /* article ground */
    #map {
        /* border: 3px solid red; */
        display: flex;
        flex-direction: row;
        justify-content: center;
        align-items: center;
        padding: 10px;
        gap: 5px;
    }

    #ground {
        /* border: 3px solid green; */
        width: 65%;
        height: 100%;
        display: grid;
        gap: 5px;
        grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
    }

    #ground div {
        border-radius: 10px;
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: flex-end;
        align-items: center;
        padding-bottom: 10px;
    }

    #contain-riv {
        background: url(../public/map/riviere.jpg);
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
    }

    #contain-for {
        background: url(../public/map/foret.jpg);
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
    }

    #contain-mon {
        background: url(../public/map/montagne.jpg);
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
    }

    #contain-mar {
        background: url(../public/map/marais.jpg);
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
    }

    #contain-des {
        background: url(../public/map/desert.svg);
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
    }

    #contain-mer {
        background: url(../public/map/mer.jpg);
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
    }


    /* ------------------------ Article Inventaire --------------------------- */
    #stuff {
        /* border: 3px solid blue; */
        width: 35%;
        min-width: 290px;
        height: 100%;
        color: var(--tertiary-color);
        font-size: clamp(1rem, 1.7vw, 1.8rem);
        text-align: center;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
    }

    #contain-bloc1 {
        /* border: 3px solid rgb(255, 97, 6); */
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    #compteur-fouille {
        color: var(--primary-color);
    }

    #contain-objet-trouve {
        background-color: var(--secondary-color);
        color: var(--fourth-color);
        width: 100%;
        height: 100%;
        text-align: center;
        font-size:  clamp(1rem, 1.3vw, 1.6rem);
        border-radius: 10px 10px 0 0;
    }

    #objet {
        display: block;
        width: 100%;
        font-size: clamp(1.4rem, 2vw, 2.4rem);
        font-weight: 700;
        background-color: var(--sixth-color);
    }

    #contain-caracteristiques, #contain-points {
        /* border: 2px solid green; */
        display: flex;
        justify-content: space-evenly;
        align-items: center;
        font-size:  clamp(1rem, 2vw, 1.8rem);
        color: #46301e;
    }

    .purple-big{
        color: rgb(156, 0, 156);
        font-size:  clamp(1.2rem, 2.1vw, 2rem);
        font-weight: 600;
    }

    .contain-btns-bloc1 {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 15px;
        margin-top: 10px;
        padding-bottom: 15px;
    }

    #contain-inventaire {
        background-color: var(--fourth-color);
        color: var(--secondary-color);
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        gap: 15px;
        padding: 15px;
        border-radius: 0 0 10px 10px;
    }

    .slot {
        background-color: var(--tertiary-color);
        height: fit-content;
        min-height: 32px;
        width: 100%;
        border-radius: 10px;
        display: flex;
        justify-content: center;
        align-items: stretch;
        text-wrap: nowrap;
        color: black;
    }

    .number-order {
        background-color: var(--sixth-color);
        color: var(--fourth-color);
        height: 100%;
        width: 8%;
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 10px 0 0 10px;
    }

    .item-in-slot {
        width: 100%;
        height: 100%;
        border-radius: 0 10px 10px 0;
        padding: 0 15px;
        display: flex;
        justify-content: space-between;
        font-size: clamp(0.7rem, 1.5vw, 1.4rem);
        padding-bottom: 5px;
    }

    .purple {
        color: #9C009C;
        font-size: clamp(1rem, 1.5vw, 1.4rem);
        font-weight: 600;
    }

    #contain-score-game {
        background-color: var(--secondary-color);
        font-size: clamp(1rem, 1.3vw, 1.6rem);
        width: 100%;
        color: #2c1d10;
        border-radius: 10px 10px 10px 10px;
    }

    .full {
        background-color: var(--secondary-color);
    }

    #contain-score-game-finish {
        background-color: var(--secondary-color);
        font-size:  clamp(1rem, 1.3vw, 1.6rem);
        height: 33%;
        width: 100%;
        color: #2c1d10;
        border-radius: 10px 10px 10px 10px;
    }

    #contain-score-game-finish p{
        margin-bottom: 85px;
    }




    /* ----------------------------- Media query ------------------------------*/
    @media screen and (max-width: 610px) {

        #ground {
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        }
    }

    @media screen and (max-width: 720px) {

        .contain-btns-bloc1 {
            flex-direction: column;
        }
    }

</style>
