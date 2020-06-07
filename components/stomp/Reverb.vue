<template>
    <div class="pedal reverb">
        <div class="controls">
            <Knob :min="0" :max="1" label="mix" @change="setMix" :default-value="0.3"/>
        </div>
        <div class="title-top">PJAMP</div>
        <Toggle :turned-on="turnedOn" @toggle="toggle"/>
        <div class="title-bottom">
            Chashitsu Reverb
        </div>
    </div>
</template>

<script>
    import ctx from "~/webAudio/audio-context.js";


    import Knob from '../elements/Knob';
    import Toggle from "../elements/Toggle";

    export default {
        name: "Reverb",
        components: {Knob, Toggle},
        props: ['input'],
        mounted() {
            fetch('/audio/greek_rev.wav')
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
                convNode: null,
                outNode: null,
                dryNode: null,
                convVolume: null,
                mix: null,
                wetNode: null,
                impulse: null,
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
                console.log(this.mix);
                if (this.dryNode && this.wetNode) {
                    this.wetNode.gain.value =value;
                    this.dryNode.gain.value = 1 - value;
                }
            },
            build() {
                if (!this.inNode && !this.outNode) {
                    this.inNode = ctx.createGain();
                    this.outNode = ctx.createGain();
                    this.convNode = ctx.createConvolver();
                    this.convVolume = ctx.createGain();
                    this.convVolume.gain.value = 3;
                    this.dryNode = ctx.createGain();
                    this.wetNode = ctx.createGain();
                    this.wetNode.gain.value = this.mix;
                    this.dryNode.gain.value = 1 - this.mix;
                    this.convNode.buffer = this.impulse;
                }
                this.inNode.connect(this.dryNode).connect(this.outNode);
                this.inNode.connect(this.convNode).connect(this.convVolume).connect(this.wetNode).connect(this.outNode);
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
