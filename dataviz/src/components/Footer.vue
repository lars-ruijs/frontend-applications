<template>
    <footer>
        <p>Bron gebruikte data: <a href="https://opendata.rdw.nl/" target="_blank">RDW</a>. <br> Bron afbeelding: <a href="https://unsplash.com/photos/ulsQefp-iwg" target="_blank">Unsplash</a>. <br> © 2020 Lars Ruijs</p>
    </footer>
</template>

<script>
export default {
    name: "Footer",
}
</script>

<style scoped>
    footer {
        height: 2em;
        border-top: 1px solid #c2c2c2;        
        margin: 6em -15.55vw 2em -15.55vw;
        text-align: center;
        padding: 2em 0 2em 0;
    }
    p {
        color: grey;
    }
</style>