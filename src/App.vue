<template>
    <AppLogo />
    <AppEq :volume="volume" />
    <AppSlider :value="threshold" @change="adjustThreshold($event, data)" />
    <AppEmoji :volume="volume" :threshold="threshold" />
    <AppTimer :volume="volume" />
</template>

<script>
import AppEmoji from "./components/AppEmoji.vue";
import AppEq from "./components/AppEq.vue";
import AppLogo from "./components/AppLogo.vue";
import AppTimer from "./components/AppTimer.vue";
import AppSlider from "./components/AppSlider.vue";

import { TALKING } from "./config";

export default {
    components: {
        AppEmoji,
        AppEq,
        AppLogo,
        AppSlider,
        AppTimer
    },

    data() {
        return {
            analyser: undefined,
            dataArray: undefined,
            threshold: 2,
            rawVolume: 0
        };
    },

    computed: {
        volume() {
            const volume = Math.floor(this.rawVolume / 16);
            if (volume > TALKING) {
                return volume + this.threshold;
            }
            return volume;
        }
    },

    methods: {
        adjustThreshold(data) {
            this.threshold = data;
        },

        handleAudioStream(stream) {
            const audioContext = new (window.AudioContext || window.webkitAudioContext)();
            const microphone = audioContext.createMediaStreamSource(stream);

            this.analyser = audioContext.createAnalyser();
            this.analyser.fftSize = 256;

            microphone.connect(this.analyser);
            this.dataArray = new Uint8Array(this.analyser.frequencyBinCount);

            this.getVolume();
        },

        getVolume() {
            if (!this.analyser) {
                return 0;
            }
            this.analyser.getByteFrequencyData(this.dataArray);

            let values = 0;
            for (let i = 0; i < this.dataArray.length; i++) {
                values += this.dataArray[i];
            }

            this.rawVolume = Math.ceil(values / this.dataArray.length);

            requestAnimationFrame(() => {
                this.getVolume();
            });
        }
    },

    mounted() {
        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
            navigator.mediaDevices
                .getUserMedia({ audio: true })
                .then((stream) => this.handleAudioStream(stream))
                .catch(function (err) {
                    console.error("Fel vid åtkomst till mikrofonen: ", err);
                });
        } else {
            console.error("Din webbläsare stödjer inte getUserMedia.");
        }
    }
};
</script>
