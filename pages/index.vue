<template>
  <Amp/>
</template>

<script>
import Amp from '~/components/Amp'

export default {
  components: {
    Amp
  }
}
</script>

<style>
  @font-face {
    font-family: "Moyko";
    src:  url('~assets/fonts/Moyko.ttf') format("truetype");
  }

  body {
    background-color: #ede9e1;
    font-family: Moyko;
    letter-spacing: 3px;
  }
  .container {
    min-height: calc(100vh - 100px);
    display: flex;
    flex-wrap: wrap;
  }

  .pedal {
    width: 186px;
    height: 320px;
    padding: 15px;
    border-radius: 10px;
    margin: 30px;
    display: flex;
    color: white;
    flex-direction: column;
    position: relative;
    box-sizing: content-box;
  }

  .pedal::after {
    background-color: #9d9c9c;
    position: absolute;
    left: -10px;
    top: 141px;
    height: 40px;
    width: 10px;
    content: '';
    z-index: -9;
  }

  .pedal::before {
    background-color: #9d9c9c;
    position: absolute;
    right: -10px;
    top: 141px;
    height: 40px;
    width: 10px;
    content: '';
    z-index: -9;
  }

  .controls {
    height: 130px;
    display: flex;
    justify-content: center;
  }
  .title-top {
    font-size: 30px;
    text-align: center;
  }
  .button-row {
    text-align: center;
  }
  .button-row button {
    height: 40px;
    width: 40px;
    border-radius: 20px;
    cursor: pointer;
    margin: 0 auto;
    border: 4px solid lightgrey;
    background-color: #ede9e1;
    outline: none;
  }

  .led-row {
    text-align: center;
    margin: 8px 0;
  }

  .knob {
    padding: 10px;
    width: 77px;
    background-image: url('~assets/img/knob-bg.png');
    height: 75px;
    background-size: 98px;
    box-sizing: content-box;
  }
  .rotary {
    background-image: url('~assets/img/knob-w-bg.png');
    height: 75px;
    width: 75px;
    margin-top: 4px;
    background-size: 75px
  }

  .led {
    display: inline-block;
    height: 15px;
    border-radius: 10px;
    width: 15px;
    background-color: #ea7c7c;
    border: 2px solid black;
  }
  .led.on {
    background-color: red;
  }

  .title-bottom {
    text-align: center;
    font-size: 25px;
    margin-top: 15px;
  }

  .overdrive {
    background-color: #562237;
  }
  .reverb {
    background-color: #d98f0d;
  }
  .compressor {
    background-color: #e34c2f;
  }
  .autowah {
    background-color: #53657e;
  }
  .amp {
    background-color: #5d7e4e;
  }
</style>
