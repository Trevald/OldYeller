<template>
    <div class="light" :class="classList">
        <span class="sr-only">{{ value }}</span>
    </div>
</template>

<script>
export default {
    props: {
        index: {
            type: Number,
            required: true
        },
        value: {
            type: Number,
            required: true
        }
    },

    computed: {
        classList() {
            const classList = [this.type];
            if (this.on) {
                classList.push("on");
            }

            return classList;
        },

        on() {
            return this.index < this.value;
        },

        type() {
            return this.index < 3 ? "low" : this.index < 6 ? "medium" : "high";
        }
    }
};
</script>
