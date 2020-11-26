<template>
    <header>
        <h1>De auto <s>(in)</s> buiten de stad</h1>
    </header>
</template>

<script>
export default {
    name: "Header",
}
</script>

<style scoped>
    header {
        background: url(../assets/centrum-parkeren-amsterdam.jpg);
        background-repeat: no-repeat;
        background-size: cover;
        background-position: center;
        background-attachment: fixed;
        height: 25em;
        margin: -0.5em -15.55vw 6em -15.55vw;
        display: flex;
        flex-direction: column;
        justify-content: center;
        border-bottom: 10px solid #325F98;
    }

    h1, p {
        text-align: center;
        color: #fff;
    }
    h1 {
        font-size: 3em;
    }
</style>