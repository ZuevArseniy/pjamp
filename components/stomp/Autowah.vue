<template>
    <div class="pedal autowah">
        <div class="controls">
            <Knob label="speed" @change="setSpeed" :max="80" :min="10" :default-value="20"/>
            <Knob label="depth" @change="setMix" :min="0" :max="1" :default-value="0.7"/>
        </div>
        <div class="title-top">PJAMP</div>
        <Toggle :turned-on="turnedOn" @toggle="toggle"/>
        <div class="title-bottom">
            Ahiru <br/>Autowah
        </div>
    </div>
</template>

<script>
    import ctx from "~/webAudio/audio-context.js";


    import Knob from '../elements/Knob';
    import Toggle from "../elements/Toggle";
    export default {
        name: "AutoWah",
        components: {Knob, Toggle},
        props: ['input'],
        data() {
            return {
                inNode: null,
                outNode: null,
                dryNode: null,
                mix: null,
                minFreq: 300,
                maxFreq: 1600,
                down: true,
                speed: null,
                wetNode: null,
                filter: null,
                filterGain: null,
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

                    this.filterGain = ctx.createGain();
                    this.filterGain.gain.value = 2;
                    this.dryNode = ctx.createGain();
                    this.wetNode = ctx.createGain();
                    this.wetNode.gain.value = this.mix;
                    this.dryNode.gain.value = 1 - this.mix;

                    this.filter = ctx.createBiquadFilter();
                    this.filter.frequency.value = 1150;
                    this.filter.type = 'bandpass';
                    this.filter.q = 1000;
                    this.filter.gain.value = -40;
                    setInterval(this.moveFreq, 3);
                }
                this.inNode.connect(this.dryNode).connect(this.outNode);
                this.inNode.connect(this.filter).connect(this.filterGain).connect(this.wetNode).connect(this.outNode);
            },
            moveFreq() {
                let currentFreq = this.filter.frequency.value;
                if (this.down) {
                    if (currentFreq <= this.minFreq) {
                        this.down = !this.down;
                        this.filter.frequency.value = currentFreq+this.speed
                    } else {
                        this.filter.frequency.value = currentFreq-this.speed;
                    }
                } else {
                    if (currentFreq >= this.maxFreq) {
                        this.down = !this.down;
                        this.filter.frequency.value = currentFreq-this.speed
                    } else {
                        this.filter.frequency.value = currentFreq+this.speed;
                    }
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
