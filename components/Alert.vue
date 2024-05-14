<template>
    <div class="alert">
        <p>{{ message }}</p>
    </div>
</template>

<script>
export default {
    props: {
        message: {
            type: String,
            required: true
        }
    }
}
</script>

<style scoped>
.alert {
    width: fit-content;
    font-size: 1.2rem;
    margin: auto;
    z-index: 1;
    margin-top: 1rem;
    margin-bottom: -1rem
}

.alert p{
    padding: 0;
    margin: 0
}
</style>