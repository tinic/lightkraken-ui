<template>
  <div>
    <div class="status">
    <span class="terminal">Terminal A
      <div v-bind:key="pin.id" 
           v-for="(pin, index) in pinTable[settings.outputmode][stripTypes[settings.stripconfig[0].type].clock].slice(0, 4)">
        <span class="PIN" :class="pin.c">{{ index }}<br />{{ pin.t }}</span>
      </div>
    </span>
    <span class="terminal">Terminal B
      <div v-bind:key="pin.id" 
           v-for="(pin,index) in pinTable[settings.outputmode][stripTypes[settings.stripconfig[1].type].clock].slice(4, 8)">
        <span class="PIN" :class="pin.c">{{ index+4 }}<br />{{ pin.t }}</span>
      </div>
    </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "PinLayout",
  props: {
    settings: Object,
    stripTypes: Array,
    pinTable: Array
  }
};
</script>

<style scoped>
.status {
  height: 120px;
  align-content: center;
}

.terminal {
  font-weight: bold;
  font-size: 20px;
  width: 314px;
  color: black;
  background-color: #eeeeee;
  float: left;
  text-align: center;
  margin: 10px;
  padding-left: 25px;
  padding-right: 25px;
  padding-top: 5px;
  padding-bottom: 5px;
  border-top: 1px solid black;
  border-bottom: 1px solid black;
}

.PIN {
  font-family: monospace;
  font-weight: bold;
  font-size: 20px;
  width: 50px;
  float: left;
  text-align: center;
  margin: 10px;
  padding: 2px;
}
.WHT {
  color: white;
  background-color: #aaaaaa;
}
.CLK {
  color: purple;
  background-color: #bbbbbb;
}
.BLU {
  color: blue;
  background-color: #bbbbbb;
}
.RED {
  color: red;
  background-color: #bbbbbb;
}
.GRN {
  color: green;
  background-color: #bbbbbb;
}
.VCC {
  color: red;
  background-color: darkred;
}
.DAT {
  color: yellow;
  background-color: #bbbbbb;
}
.NIL {
  color: grey;
  background-color: #bbbbbb;
}
.GND {
  color: black;
  background-color: #bbbbbb;
}

</style>
