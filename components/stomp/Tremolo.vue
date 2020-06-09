<template>
    <div class="pedal tremolo">
        <div class="controls">
            <Knob :min="0" :max="1" label="mix" @change="setMix" :default-value="0.7"/>
            <Knob :min="1" :max="10" label="speed" @change="setSpeed" :default-value="3"/>
        </div>
        <div class="title-top">PJAMP</div>
        <Toggle :turned-on="turnedOn" @toggle="toggle"/>
        <div class="title-bottom">
            Myakudo Tremolo
        </div>
    </div>
</template>

<script>
    import ctx from "~/webAudio/audio-context.js";


    import Knob from '../elements/Knob';
    import Toggle from "../elements/Toggle";

    export default {
        name: "Tremolo",
        components: {Knob, Toggle},
        props: ['input'],
        data() {
            return {
                inNode: null,
                outNode: null,
                dryNode: null,
                tremoloGain: null,
                mix: null,
                speed: null,
                wetNode: null,
                oscillator: null,
                turnedOn: false,
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
            setMix(value) {
                this.mix = value;
                if (this.dryNode && this.wetNode) {
                    this.wetNode.gain.value = value;
                    this.dryNode.gain.value = 1 - value;
                }
            },
            setSpeed(value) {
                this.speed = value;
                if (this.oscillator) {
                    this.oscillator.frequency.value = value;
                }
            },
            build() {
                if (!this.inNode && !this.outNode) {
                    this.inNode = ctx.createGain();
                    this.outNode = ctx.createGain();

                    this.oscillator = ctx.createOscillator();
                    this.tremoloGain = ctx.createGain();
                    this.dryNode = ctx.createGain();
                    this.wetNode = ctx.createGain();
                    this.oscillator.frequency.value = this.speed;
                    this.oscillator.connect(this.tremoloGain.gain);
                    this.oscillator.start();
                    this.wetNode.gain.value = this.mix;
                    this.dryNode.gain.value = 1 - this.mix;
                }
                this.inNode.connect(this.dryNode).connect(this.outNode);
                this.inNode.connect(this.tremoloGain).connect(this.wetNode).connect(this.outNode);
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
 .tremolo {
     background-color: #c6233e;
 }
</style>
