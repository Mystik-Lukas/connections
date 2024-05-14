<template>
    <div :class="{'button': true, 'disabled': disabled}">{{ label }}</div>
</template>

<script>
export default {
    props: {
        label: {
            type: String,
            required: true
        },
        disabled: {
            type: Boolean,
            required: true
        }
    }
}
</script>

<style scoped>
.button {
    padding: 7px 25px;
    border-radius: 30px;
    border: 2px solid #4D4D4D;
    margin: 0 .6rem;
    cursor:pointer
}

.disabled {
    border: 2px solid #b9b9b9 !important; 
    cursor:not-allowed;
    color: #b9b9b9
}
</style>