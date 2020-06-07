<template>
    <div>
        <div class="led-row">
            <span class="led" :class="{on: turnedOn}"></span>
        </div>
        <div class="button-row">
            <button @click="toggle"></button>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Toggle",
        props: ['turnedOn'],
        methods: {
            toggle() {
                this.$emit('toggle');
            }
        }
    }
</script>

<style lang="scss" scoped>
    .led {
        &.on {
            background-color: red;
        }
    }
</style>
