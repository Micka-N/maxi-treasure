<script>
    export let textButton = "Bouton";
</script>




<!-- 🔧 HTML  ═══════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 🔧 HTML -->
<button class="button-32" on:click>{textButton}</button>




<!-- 🎨 CSS  ═════════════════════════════ ⬇︎ ⬇︎ ⬇︎ ═════════════════════════════ 🎨 CSS -->

<style>
    
.button-32 {
    background-color: var(--primary-color);
    border-radius: 12px;
    color: var(--fourth-color);
    cursor: pointer;
    font-weight: bold;
    padding: 10px 15px;
    text-align: center;
    transition: 200ms;
    width: max-content;
    box-sizing: border-box;
    border: 0;
    font-size: 16px;
    user-select: none;
    -webkit-user-select: none;
    touch-action: manipulation;
}

.button-32:not(:disabled):hover,
.button-32:not(:disabled):focus {
    outline: 0;
    background: var(--primary-color);
    box-shadow: 2px 2px 2px 2px var(--secondary-color), 2px 2px 2px 2px var(--secondary-color);
}

.button-32:not(:disabled):focus {
    background:  var(--tertiary-color);
}

.button-32:disabled {
    filter: saturate(0.2) opacity(0.5);
    -webkit-filter: saturate(0.2) opacity(0.5);
    cursor: not-allowed;
}

</style>
