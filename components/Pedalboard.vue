<template>
<div class="container">
    <Overdrive :input="this.input" @output="onOverdriveOutput" />
    <Tremolo :input="this.tremoloInput" @output="onTremoloOutput"/>
    <Autowah :input="this.autowahInput" @output="onAutoWahOutput"/>
    <Reverb :input="this.reverbInput" @output="onReverbOutput"/>
    <Cabinet :input="this.cabinetInput" @output="onCabinetOutput" />
</div>
</template>

<script>

    import Cabinet from "./stomp/Cabinet";
    import Overdrive from "./stomp/Overdrive";
    import Reverb from "./stomp/Reverb";
    import Tremolo from "./stomp/Tremolo";
    import Autowah from "./stomp/Autowah";

    export default {
        name: "Pedalboard",
        props: ['input'],
        components: {Tremolo, Reverb, Overdrive, Cabinet, Autowah},
        data() {
            return {
                cabinetInput: null,
                reverbInput: null,
                tremoloInput: null,
                autowahInput: null,
            }
        },
        watch: {
            input(newVal) {
                // if (newVal !== null) {
                //     this.$emit('output', this.input);
                // }
            }
        },
        methods: {
            onTremoloOutput(stream) {
                this.autowahInput = stream;

            },
            onCabinetOutput(stream) {
                this.$emit('output', stream);
            },
            onReverbOutput(stream) {
                this.cabinetInput = stream;
            },
            onOverdriveOutput(stream) {
                this.tremoloInput = stream;
            },
            onAutoWahOutput(stream) {
                this.reverbInput = stream
            }
        }
    }
</script>

<style scoped>

</style>
