<template>
    <div>
        <Header @source="onSetSource"/>
        <Pedalboard :input="this.source" @output="onSetOutput" />
    </div>
</template>

<script>
import Header from '~/components/Header'
import Pedalboard from '~/components/Pedalboard'
import ctx from "~/webAudio/audio-context.js";

export default {
    name: "Amp",
    components: {
        Header,
        Pedalboard
    },
    data() {
        return {
            source: null,
            output: null,
        }
    },
    methods: {
        onSetSource(source) {
            this.source = source;
        },
        onSetOutput(stream) {
            if (this.output) {
                this.output.disconnect(ctx.destination);
            }
            this.output = stream;
            stream.connect(ctx.destination);
        }
    }
}
</script>

<style scoped>

</style>
