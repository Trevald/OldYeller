<template>
    <p class="timer">
        <span class="value">{{ value }}</span>
        <span class="unit">{{ unit }}</span>
    </p>
</template>

<script>
import { TALKING, WRAPITUP } from "../config.js";

export default {
    props: {
        volume: {
            type: Number,
            required: true
        }
    },

    watch: {
        volume(newValue, oldValue) {
            console.log(newValue);
            if (newValue > TALKING && this.timer === undefined) {
                return this.startTimer();
            }

            if (newValue < TALKING && this.timer !== undefined) {
                return this.pauseTimer();
            }
        }
    },

    data() {
        return {
            value: 0,
            timerForPause: undefined,
            timer: undefined,
            timerStarted: undefined
        };
    },

    computed: {
        unit() {
            return "s";
        }
    },

    methods: {
        startTimer() {
            this.timerStarted = Date.now();
            this.timer = setTimeout(() => {
                this.count();
            }, 1);
        },

        count() {
            this.value = Math.floor((Date.now() - this.timerStarted) / 1000);
            this.timer = setTimeout(() => {
                this.count();
            }, 1);
        },

        pauseTimer() {
            if (this.timerForPause) {
                return;
            }
            clearTimeout(this.timerForPause);
            this.timerForPause = setTimeout(() => {
                this.stopTimer();
            }, 1000);
        },

        stopTimer() {
            clearTimeout(this.timer);
            this.timer = undefined;
            clearTimeout(this.timerForPause);
            this.timerForPause = undefined;
            this.value = 0;
        }
    }
};
</script>
