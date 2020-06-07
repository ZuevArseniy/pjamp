<template>
    <div class="knob" ref="knob">
        <div class="rotary" @mousedown="engage" ref="rotary"></div>
        <label>{{label}}</label>
    </div>
</template>

<script>

    export default {
        name: "Knob",
        props: {
            min: {
                type: Number,
                default: 0,
            },
            max: {
                type: Number,
                default: 100,
            },
            label: {
                type: String,
                default: 'label'
            },
            defaultValue: {
                type: Number
            }
        },
        computed: {
            value() {
                return this.min + (Math.abs(this.max - this.min)*this.rotatePercent);
            },
            rotatePercent() {
                return Math.abs(this.rotatedDeg - this.minDeg)/this.degRange;
            },
            degRange() {
                return Math.abs(this.maxDeg - this.minDeg);
            }
        },
        data() {
            return {
                minDeg: -150,
                maxDeg: 150,
                rotatedDeg: 0,
                diff: 0,
                prevY: null,
                engaged: false,
            }
        },
        methods: {
            engage(event) {
                this.engaged = true;
                this.prevY = event.clientY;
                event.preventDefault();
            },
            disengage(event) {
                if (this.engaged) {
                    this.rotatedDeg = this.diff;
                    this.diff = 0;
                    this.$emit('change', this.value);
                    this.engaged = false;
                }
            },
            rotaryMove(Y) {
                if (this.engaged) {
                    if (this.prevY - Y === 0) {
                        return;
                    }
                    const goingUp = this.prevY >= Y;
                    let diff = Math.abs(this.prevY-Y); //1px per 1degree rotate
                    if (goingUp) {
                        diff = diff+this.rotatedDeg;
                        if (diff > this.maxDeg) {
                            diff = this.maxDeg;
                        }
                        this.$refs.rotary.style = 'transform: rotate(' + diff + 'deg)';
                    } else {
                        diff = this.rotatedDeg-diff;
                        if (diff < this.minDeg) {
                            diff=this.minDeg;
                        }
                        this.$refs.rotary.style = 'transform: rotate(' + diff + 'deg)';
                    }
                    this.diff = diff;
                }
            },
            rotateDegByValue(value) {
                return (value/Math.abs(this.max - this.min))*(Math.abs(this.maxDeg - this.minDeg)) + this.minDeg;
            }

        },
        watch: {
            defaultValue: function(newVal, oldVal) {
                this.rotatedDeg = this.rotateDegByValue(newVal);
                this.$refs.rotary.style = 'transform: rotate(' + this.rotatedDeg  + 'deg)';
                this.$emit('change', this.value);
            }
        },
        mounted() {
            let self = this;
            window.addEventListener('mouseup', this.disengage);
            window.addEventListener('mousemove', event => {
                self.rotaryMove(event.clientY);
            });
            if (this.defaultValue) {
                this.rotatedDeg = this.rotateDegByValue(this.defaultValue);
                this.$refs.rotary.style = 'transform: rotate(' + this.rotatedDeg  + 'deg)';
                console.log(this.value);
            }
            this.$emit('change', this.value);
        }
    }
</script>

<style lang="scss" scoped>
.knob {
    text-align: center;
    label {
        display: inline-block;
        margin: 5px 0 10px 0;
    }
}
</style>
