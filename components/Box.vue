<template>
    <div :class="{'box': true, 'selected': selected(), 'disabled': disabled}" @click = "selectedMethod">
        <p>{{ label }}</p>
    </div>
</template>

<script>
export default {
    props: {
        label: {
                type: String, 
                required: true 
            },
        selectedList: {
                type: Object,
                required: true
        },
        disabled: {
            tyle: Boolean,
            required: true
        }
    },
    methods: {
        selectedMethod() {
            var found;
            for (const [key, value] of Object.entries(this.selectedList)) {
                if (this.label == key) {
                    found = true;
                }
            }
            if (Object.keys(this.selectedList).length == 4 && !found) {
                return;
            }
            this.$emit('box-clicked', this.label);
        },
        selected() {
            for (const [key, value] of Object.entries(this.selectedList)) {
                if (key == this.label) {
                    return true
                }
            }
            return false
        }
    }
};
</script>

<style scoped>

    .box {
        background-color: #D9D9D9;
        width: 7.5rem;
        height: 4.5rem;
        display: flex;
        align-items: center;
        justify-content: center;
        border-radius: 12px;
        cursor:pointer;
        font-size: .9rem
    }

    p {
        margin: 0;
        text-align: center;
    }

    .selected {
        background-color: #5B564D;
        color: white
    }

    .disabled {
        cursor: not-allowed;
        opacity: 50%
    }
</style>