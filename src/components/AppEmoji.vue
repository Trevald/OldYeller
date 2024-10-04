<template>
    <div class="emoji">{{ value }}</div>
</template>
<script>
import { TALKING, SCREAMING } from "../config.js";

export default {
    props: {
        volume: {
            type: Number,
            required: true
        }
    },

    computed: {
        value() {
            if (this.volume > SCREAMING) {
                return "📢";
            }

            if (this.volume > TALKING) {
                return "🎙️";
            }

            return "😴";
        }
    }
};
</script>
