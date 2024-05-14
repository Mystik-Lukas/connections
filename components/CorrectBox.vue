<template>
    <div class="correct-box" :class="color">
        <p class="theme">{{ theme }}</p>
        <div class="words">
            <p class="word" v-for="word in wordValues">{{ word.word }}<span v-if="word.word != wordValues[3].word">,</span></p>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        wordValues: {
                type: Array, 
                required: true 
            },
        color: {
            type: String,
            required: true
        },
        theme: {
            type: String,
            required: true
        }
    }
}
</script>

<style>
.words {
display: flex;
flex-direction: row;
gap: 5px;
justify-content: center;
font-size: 1rem;
}

.blue {
    background-color: rgb(51, 129, 255);
    color: white
}

.red {
    background-color: rgb(255, 51, 51);
    color: white
}

.green {
    background-color: rgb(76, 218, 76);
    color: white
}

.yellow {
    background-color: yellow;
    color: black
}

.theme {
    text-align: center;
    margin: 0;
    font-size: 1.2rem
}

.word {
    margin: 0
}

.correct-box {
    padding: 10px 0;
    border-radius: 15px;
    margin-bottom: 1rem;
    width: 540px
}
</style>