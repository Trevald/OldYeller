<template>
    <ul class="eq">
        <li v-for="(light, index) in lights" :key="index"><AppEqLight :index="index" :value="value" /></li>
    </ul>
</template>

<script>
import AppEqLight from "./AppEqLight.vue";

export default {
    components: {
        AppEqLight
    },

    props: {
        volume: {
            type: Number,
            required: true
        }
    },

    data: () => ({
        numberOfLights: 12
    }),

    computed: {
        value() {
            return Math.round(this.volume / (16 / this.numberOfLights));
        },

        lights() {
            const lights = [];
            for (let i = 0; i < this.numberOfLights; i++) {
                lights.push({
                    type: this.getLightType(i),
                    on: i < this.volume / (255 / this.numberOfLights)
                });
            }
            return lights;
        }
    },

    methods: {
        getLightType(index) {
            if (index < 3) {
                return "low";
            } else if (index < 6) {
                return "medium";
            } else if (index < 9) {
                return "high";
            } else {
                return "neutral";
            }
        }
    }
};
</script>
