<template>
    <div class="pedal amp">
        <div class="controls">
            <Knob :min="0" :max="7" label="volume" @change="setVolume" :default-value="4"/>
        </div>
        <div class="title-top">PJAMP</div>
        <Toggle :turned-on="turnedOn" @toggle="toggle"/>
        <div class="title-bottom">
            Origami Cabinet
        </div>
    </div>
</template>

<script>
    import ctx from "~/webAudio/audio-context.js";


    import Knob from '../elements/Knob';
    import Toggle from "../elements/Toggle";
    export default {
        components: {
            Toggle,
            Knob
        },
        name: "Cabinet",
        props: ['input'],
        mounted() {
            fetch('audio/impulse.wav')
                .then(response => response.arrayBuffer())
                .then(buffer => {
                    ctx.decodeAudioData(buffer, decoded => {
                        this.impulse = decoded;
                    })
                .catch((err) => console.error(err));
            });
        },
        data() {
            return {
                inNode: null,
                outNode: null,
                impulse: null,
                turnedOn: false,
                volume: 2,
            }
        },
        watch: {
            input(newVal, oldVal) {
                if (newVal !== null && newVal !== oldVal) {
                    if (oldVal !== null && this.turnedOn) {
                        oldVal.disconnect(this.inNode);
                    }
                    this.init();
                }
            }
        },
        methods: {
            build() {
                if (!this.inNode && !this.outNode) {
                    this.inNode = ctx.createConvolver();
                    this.outNode = ctx.createGain();
                    this.outNode.gain.value = this.volume;
                    this.inNode.buffer = this.impulse;
                }
                this.inNode.connect(this.outNode);
            },
            setVolume(volume) {
                this.volume = volume;
                if (this.outNode) {
                    this.outNode.gain.value = volume;
                }
            },
            turnOn() {
                this.build();
                this.input.connect(this.inNode);
                this.$emit('output', this.outNode);
            },
            turnOff() {
                this.input.disconnect(this.inNode);
                this.$emit('output', this.input);
            },
            toggle() {
                this.turnedOn = !this.turnedOn;
                this.init()
            },
            init() {
                this.turnedOn ? this.turnOn() : this.turnOff();
            }
        }
    }
</script>

<style scoped>

</style>
