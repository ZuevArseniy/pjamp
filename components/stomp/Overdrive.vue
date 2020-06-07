<template>
    <div class="pedal overdrive">
        <div class="controls">
            <Knob :min="0" :max="400" label="effect" @change="setEffect" :default-value="150"/>
            <knob :min="0" :max="1" label="volume" @change="setVolume" :default-value="0.5"/>
        </div>
        <div class="title-top">PJAMP</div>
        <Toggle :turned-on="turnedOn" @toggle="toggle" />
        <div class="title-bottom">
            Sacura Overdrive
        </div>
    </div>
</template>

<script>
    import ctx from "~/webAudio/audio-context.js";

    import Knob from '../elements/Knob';
    import Toggle from "../elements/Toggle";
    export default {
        name: "Overdrive",
        props: ['input'],
        components: {Toggle, Knob},
        data() {
            return {
                overdriveInNode: null,
                overdriveOutNode: null,
                effect: null,
                volume: null,
                turnedOn: false
            }
        },
        watch: {
            input(newVal, oldVal) {
                if (newVal !== null && newVal !== oldVal) {
                    if (oldVal !== null && this.turnedOn) {
                        oldVal.disconnect(this.overdriveInNode);
                    }
                    this.init();
                }
            }
        },
        methods: {
            setEffect(effect) {
                this.effect = effect;
                if (this.overdriveInNode) {
                    this.overdriveInNode.curve = this.makeDistortionCurve(effect);
                }
            },
            setVolume(volume) {
                this.volume = volume;
                if (this.overdriveOutNode) {
                    this.overdriveOutNode.gain.value = volume;
                }
            },
            makeOverdrive() {
                if (!this.overdriveInNode && !this.overdriveOutNode) {
                    let distortionGainNode = ctx.createGain();
                    let distortionNode = ctx.createWaveShaper();
                    distortionGainNode.gain.value = this.volume;
                    distortionNode.curve = this.makeDistortionCurve(this.effect);
                    this.overdriveInNode = distortionNode;
                    this.overdriveOutNode = distortionGainNode;
                    this.overdriveInNode.connect(this.overdriveOutNode);
                }
            },
            makeDistortionCurve(amount) {
                let k = typeof amount === 'number' ? amount : 50,
                    n_samples = 44100,
                    curve = new Float32Array(n_samples),
                    deg = Math.PI / 180,
                    i = 0,
                    x;
                for (; i < n_samples; ++i) {
                    x = (i * 2) / n_samples - 1;
                    curve[i] = ((3 + k) * x * 20 * deg) / (Math.PI + k * Math.abs(x));
                }
                return curve;
            },
            turnOn() {
                this.makeOverdrive();
                this.input.connect(this.overdriveInNode);
                this.$emit('output', this.overdriveOutNode);
            },
            turnOff() {
                this.input.disconnect(this.overdriveInNode);
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
