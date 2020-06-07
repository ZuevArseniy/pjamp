<template>
<div class="container">
    <Overdrive :input="this.input" @output="onOverdriveOutput" />
    <Compressor/>
    <Reverb :input="this.reverbInput" @output="onReverbOutput"/>
    <Cabinet :input="this.cabinetInput" @output="onCabinetOutput" />
</div>
</template>

<script>

    import Cabinet from "./stomp/Cabinet";
    import Overdrive from "./stomp/Overdrive";
    import Reverb from "./stomp/Reverb";
    import Compressor from "./stomp/Compressor";
    export default {
        name: "Pedalboard",
        props: ['input'],
        components: {Compressor, Reverb, Overdrive, Cabinet},
        data() {
            return {
                cabinetInput: null,
                reverbInput: null,
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
            onCabinetOutput(stream) {
                this.$emit('output', stream);
            },
            onReverbOutput(stream) {
                this.cabinetInput = stream;
            },
            onOverdriveOutput(stream) {
                this.reverbInput = stream;
            }
        }
    }
</script>

<style scoped>

</style>
