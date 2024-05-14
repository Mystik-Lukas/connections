<template>
    <div :class="{'dot': true, 'used': this.used}"></div>
</template>

<script>
export default {
    props: {
        used: {
                type: Boolean, 
                required: false 
            }
    },
}
</script>

<style>
.dot {
    height: 20px;
    width: 20px;
    background-color: #4D4D4D;
    border-radius: 50%;
    margin: .3rem
}

.used {
    opacity: 25% !important;
}
</style>