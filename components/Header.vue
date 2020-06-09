<template>
    <header>
        <div class="left-block">
            <img class="logo" src="~assets/img/logo.png"/>
            <span class="title">PJAMP</span>
        </div>
        <div class="dd-column">
            <label >Source</label>
            <select v-on:change="selectSource($event)">
                <option value="samples">samples</option>
                <option value="mic">audio input</option>
            </select>
        </div>
        <div class="toggle" :class="{on: turnedOn}" @click="toggle"></div>
    </header>
</template>

<script>
    import ctx from "~/webAudio/audio-context.js";

    let convertToMono = function(input) {
        let splitter = ctx.createChannelSplitter(2);
        let merger = ctx.createChannelMerger(2);

        input.connect(splitter);
        splitter.connect(merger, 0, 0);
        splitter.connect(merger, 0, 1);
        return merger;
    };

    export default {
        name: "Header",
        props: ['ctx1'],
        data() {
            return {
                turnedOn: false,
                sampleBuffer: null,
                source: null,
                inputStream: null,
                inputSource: null,
                sampleSource: null,
            }
        },
        mounted() {
            fetch('audio/demo.mp3')
                .then(response => response.arrayBuffer())
                .then(arrayBuffer => ctx.decodeAudioData(arrayBuffer))
                .then(audioBuffer => {
                    this.sampleBuffer = audioBuffer;
                })
                .then(this.selectSource);
        },
        methods: {
            toggle() {
                this.turnedOn = !this.turnedOn;
                this.turnedOn ? ctx.resume() : ctx.suspend();
            },
            async selectSource(event) {
                let source;
                if (event && event.target.value === 'mic') {
                    if (!this.inputStream) {
                        this.inputStream = await navigator.mediaDevices.getUserMedia({ audio: {
                                mandatory: {echoCancellation: false}
                            }, video: false });
                        this.inputSource = convertToMono(ctx.createMediaStreamSource(this.inputStream));
                    }
                    source = this.inputSource;
                } else {
                    if (!this.sampleSource) {
                        this.sampleSource = ctx.createBufferSource();
                        this.sampleSource.buffer = this.sampleBuffer;
                        this.sampleSource.loop = true;
                        this.sampleSource.start();
                    }
                    source = this.sampleSource;
                }
                this.$emit('source', source);
            }
        }
    }
</script>

<style lang="scss" scoped>
    header {
        background-image: url("~assets/img/golden.jpg");
        background-size: 100%;
        padding: 20px 50px;
        height: 100px;
        display: flex;
        align-items: center;
        font-size: 50px;
        box-sizing: content-box;

        .left-block {
            display: flex;
            align-items: center;

            .logo {
                height: 100px;
                width: 100px;
            }
            .title {
                font-size: 50px;
                margin-left: 30px;
            }
        }

        .dd-column {
            margin-left: 60px;
            label {
                display: block;
                font-size: 30px;
            }
            select {
                font-size: 30px;
                height: 40px;
            }
        }

        .toggle {
            background-image: url('~assets/img/toggle-off.png');
            height: 100px;
            width: 52px;
            cursor: pointer;
            background-size: 52px;
            border-radius: 4px;
            margin-left: auto;

            &.on {
                background-image: url('~assets/img/toggle-on.png');
            }
        }
    }

</style>
