<template>
    <div class="mistakes-remaining">
        <p>Mistakes remaining: </p>
        <div v-for="guess in getGuesses()">
            <Dot :used="false"/>
        </div>
        <div v-for="guess in getGuessesUsed()">
            <Dot :used="true"/>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        guesses: {
                type: Number, 
                required: true 
            }
    },
    methods: {
        getGuesses() {
            return Array.from({ length: this.guesses }, () => null);
        },
        getGuessesUsed() {
            return Array.from({ length: 4-this.guesses }, () => null);
        }
    }
}
</script>

<style>
.mistakes-remaining {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 1rem
}

.mistakes-remaining p {
    margin-right: .8rem
}
</style>