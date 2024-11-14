<template>
    <div class="slider">
        <input type="range" :value="value" min="0" max="12" step="1" @input="input($event)" />
    </div>
</template>

<script>
export default {
    emits: ["change"],

    props: {
        value: {
            type: Number,
            default: 50
        }
    },

    methods: {
        input(event) {
            this.$emit("change", parseInt(event.target.value));
        }
    }
};
</script>
