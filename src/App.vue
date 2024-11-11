<template>
    <AppLogo />
    <AppEq :volume="volume" />
    <AppEmoji :volume="volume" />
    <AppTimer :volume="volume" />
</template>

<script>
import AppEmoji from "./components/AppEmoji.vue";
import AppEq from "./components/AppEq.vue";
import AppLogo from "./components/AppLogo.vue";
import AppTimer from "./components/AppTimer.vue";

export default {
    components: {
        AppEmoji,
        AppEq,
        AppLogo,
        AppTimer
    },

    data() {
        return {
            analyser: undefined,
            dataArray: undefined,
            limit: 16,
            rawVolume: 0
        };
    },

    computed: {
        volume() {
            return Math.floor(this.rawVolume / 16);
        }
    },

    methods: {
        handleAudioStream(stream) {
            const audioContext = new (window.AudioContext || window.webkitAudioContext)();

            // Skapa en källnod för mikrofonströmmen
            const microphone = audioContext.createMediaStreamSource(stream);

            // Skapa en analyseringsnod
            this.analyser = audioContext.createAnalyser();
            this.analyser.fftSize = 256;

            // Koppla mikrofonen till analysatorn
            microphone.connect(this.analyser);

            // Skapa en array för att lagra frekvensdata
            this.dataArray = new Uint8Array(this.analyser.frequencyBinCount);

            this.getVolume();
        },

        getVolume() {
            if (!this.analyser) {
                return 0;
            }
            this.analyser.getByteFrequencyData(this.dataArray);

            // Beräkna genomsnittet av frekvensdata (där volymen mäts)
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
            // Få tillgång till mikrofonen
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
