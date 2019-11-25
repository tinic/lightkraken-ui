<template>
  <div>
    <div class="status">
    <span class="terminal">{{ terminalNames[0] }}
      <div v-bind:key="pin.id" 
           v-for="(pin, index) in pinTable[settings.outputconfig][stripOutputTypes[settings.stripconfig[0].outputtype].clock].slice(0, 4)">
        <div class="PIN" :class="pin.c">{{ index }}<br />{{ pin.t }}</div>
      </div>
    </span>
    <span class="terminal">{{ terminalNames[1] }}
      <div v-bind:key="pin.id" 
           v-for="(pin,index) in pinTable[settings.outputconfig][stripOutputTypes[settings.stripconfig[1].outputtype].clock].slice(4, 8)">
        <div class="PIN" :class="pin.c">{{ index+4 }}<br />{{ pin.t }}</div>
      </div>
    </span>
    <div class="spacer" style="clear: both;"></div>
    </div>
    <div class="spacer" style="clear: both;"></div>
  </div>
</template>

<script>
export default {
  name: "PinLayout",
  props: {
    settings: Object,
    terminalNames: Array,
    stripOutputTypes: Array,
    pinTable: Array
  },
  methods: {
      isMobile() {
        if(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
          return true
        } else {
          return false
        }
      }
  }
};
</script>

<style scoped>
.status {
  max-width: 800px;
  align-content: center;
}

.terminal {
  display: inline-block;
  font-weight: 600;
  font-size: 20px;
  min-width: 300px;
  color: black;
  background-color: #eeeeee;
  text-align: center;
  margin: 10px;
  border-radius: 16px;
  padding: 4px;
}

.PIN {
  font-family: monospace;
  font-weight: bold;
  font-size: 20px;
  width: 50px;
  float: left;
  clear: right;
  text-align: center;
  margin: 6px;
  padding: 6px;
  border-radius: 16px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.PIN_mobile {
  font-family: monospace;
  font-weight: bold;
  font-size: 15px;
  width: 35px;
  float: left;
  text-align: center;
  margin: 1px;
  padding: 1px;
  border-radius: 4px;
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
