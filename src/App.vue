<template>
  <div id="app">
    <div v-if="this.statusloading === false">
      <Status
        v-bind:status="status"/>
    </div>
    <div v-if="this.settingsloading === false">
      <PinLayout
        v-bind:terminalNames="terminalNames"
        v-bind:stripTypes="stripTypes"
        v-bind:settings="settings"
        v-bind:pinTable="pinTable"/>
      <NetworkInterface
        v-bind:settings="settings"/>
      <OutputConfig
        v-bind:outputModes="outputModes"
        v-bind:settings="settings"/>
      <StripConfig
        v-if="this.showConfigs[this.settings.outputmode].strip[0] === true"
        v-bind:stripIndex=0
        v-bind:terminalNames="terminalNames"
        v-bind:stripTypes="stripTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <RGBConfig
        v-if="this.showConfigs[this.settings.outputmode].rgb[0] === true"
        v-bind:rgbIndex=0
        v-bind:terminalNames="terminalNames"
        v-bind:rgbTypes="rgbTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <StripConfig 
        v-if="this.showConfigs[this.settings.outputmode].strip[1] === true"
        v-bind:stripIndex=1
        v-bind:terminalNames="terminalNames"
        v-bind:stripTypes="stripTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <RGBConfig 
        v-if="this.showConfigs[this.settings.outputmode].rgb[1] === true"
        v-bind:rgbIndex=1
        v-bind:terminalNames="terminalNames"
        v-bind:rgbTypes="rgbTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <ActionBar
        v-bind:baseURL="baseURL"
        v-bind:settings="settings"/>
    </div>
  </div>
</template>

<script>
import RGBConfig from "./components/RGBConfig.vue";
import StripConfig from "./components/StripConfig.vue";
import OutputConfig from "./components/OutputConfig.vue";
import ActionBar from "./components/ActionBar.vue";
import Status from "./components/Status.vue";
import PinLayout from "./components/PinLayout.vue";
import NetworkInterface from "./components/NetworkInterface.vue";
export default {
  name: "app",
  data() {
    return {
      baseURL: 'http://192.168.1.131/',
      status: null,
      statusloading: true,
      settings: null,
      settingsloading: true,
      terminalNames: [
         "Terminal A",
         "Terminal B",
      ],
      outputModes: [
          { text: '2 x RGB Strip', value : 0 },
          { text: '1 x RGB Strip + 1 x RGB Analog', value : 1 },
          { text: '2 x RGB Strip + 1 x RGB Analog', value : 2 },
          { text: '1 x RGB Strip + 1 x RGB+W Analog', value : 3 },
          { text: '2 x RGB Analog', value : 4 },
      ],
      showConfigs: [
          { strip: [true,  true ], rgb : [false, false], clock:[1, 1], components:[0, 0] },
          { strip: [false, true ], rgb : [true,  false], clock:[0, 1], components:[3, 0] },
          { strip: [true,  true ], rgb : [true,  false], clock:[0, 0], components:[3, 0] },
          { strip: [false, true ], rgb : [true,  false], clock:[0, 0], components:[4, 0] },
          { strip: [false, false], rgb : [true,  true ], clock:[0, 0], components:[3, 3] }
      ],
      rgbTypes: [
          { text: 'RGB (8-bit, sRGB)',  value : 0,  components: 3, size:8  },
          { text: 'RGBW (8-bit, sRGB)',  value : 1,  components: 4, size:8  },
          { text: 'RGBWW (8-bit, sRGB)',  value : 2,  components: 5, size:8  },
          { text: 'RGB (16-bit, sRGB)',  value : 3,  components: 3, size:16  },
          { text: 'RGBW (16-bit, sRGB)',  value : 4,  components: 4, size:16  },
          { text: 'RGBWW (16-bit, sRGB)',  value : 5,  components: 5, size:16  },
          { text: 'RGB (16-bit, PWM Duty %)',  value : 6,  components: 3, size:16  },
          { text: 'RGBW (16-bit, PWM Duty %)',  value : 7,  components: 4, size:16  },
          { text: 'RGBWW (16-bit, PWM Duty %)',  value : 8,  components: 5, size:16  }
      ],
      stripTypes: [
          { text: 'WS2812 (RGB)',  value : 0,  clock : 0, components: 3  },
          { text: 'SK6812 (RGB)',  value : 1,  clock : 0, components: 3  },
          { text: 'TM1804 (RGB)',  value : 2,  clock : 0, components: 3  },
          { text: 'UCS1904 (RGB)', value : 3,  clock : 0, components: 3  },
          { text: 'GS8208 (RGB)',  value : 4,  clock : 0, components: 3  },
          { text: 'SK6812 (RGBW)', value : 5,  clock : 0, components: 4  },
          { text: 'APA102 (RGB)',  value : 6,  clock : 1, components: 3  },
          { text: 'APA107 (RGB)',  value : 7,  clock : 1, components: 3  },
          { text: 'P9813 (RGB)',   value : 8,  clock : 1, components: 3  },
          { text: 'SK9822 (RGB)',  value : 9,  clock : 1, components: 3  },
          { text: 'HDS107S (RGB)', value : 10, clock : 1, components: 3  },
          { text: 'LPD8806 (RGB)', value : 11, clock : 0, components: 3  },
          { text: 'TLS3001 (RGB)', value : 12, clock : 0, components: 3  },
          { text: 'TM1829 (RGB)',  value : 13, clock : 0, components: 3  }
      ],
      pinTable: [
        [ 
          [{t:"GND", c:"GND"},{t:"---", c:"NIL"},{t:"DAT0",c:"DAT"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"---", c:"NIL"},{t:"DAT1",c:"DAT"},{t:"VCC",c:"VCC"}],
          [{t:"GND", c:"GND"},{t:"CLK0",c:"CLK"},{t:"DAT0",c:"DAT"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"CLK1",c:"CLK"},{t:"DAT1",c:"DAT"},{t:"VCC",c:"VCC"}]
        ],[
          [{t:"BLU", c:"BLU"},{t:"RED", c:"RED"},{t:"GRN", c:"GRN"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"---", c:"NIL"},{t:"DAT", c:"DAT"},{t:"VCC",c:"VCC"}],
          [{t:"BLU", c:"BLU"},{t:"RED", c:"RED"},{t:"GRN", c:"GRN"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"CLK", c:"CLK"},{t:"DAT", c:"DAT"},{t:"VCC",c:"VCC"}]
        ],[
          [{t:"BLU", c:"BLU"},{t:"RED", c:"RED"},{t:"DAT0",c:"DAT"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"GRN", c:"GRN"},{t:"DAT1",c:"DAT"},{t:"VCC",c:"VCC"}],
          [{t:"BLU", c:"BLU"},{t:"RED", c:"RED"},{t:"DAT0",c:"DAT"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"GRN", c:"GRN"},{t:"DAT1",c:"DAT"},{t:"VCC",c:"VCC"}]
        ],[
          [{t:"BLU", c:"BLU"},{t:"RED", c:"RED"},{t:"GRN", c:"GRN"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"WHT", c:"WHT"},{t:"DAT", c:"DAT"},{t:"VCC",c:"VCC"}],
          [{t:"BLU", c:"BLU"},{t:"RED", c:"RED"},{t:"GRN", c:"GRN"},{t:"VCC",c:"VCC"},{t:"GND", c:"GND"},{t:"WHT", c:"WHT"},{t:"DAT", c:"DAT"},{t:"VCC",c:"VCC"}]
        ],[
          [{t:"BLU0",c:"BLU"},{t:"RED0",c:"RED"},{t:"GRN0",c:"GRN"},{t:"VCC",c:"VCC"},{t:"BLU1",c:"BLU"},{t:"RED1",c:"RED"},{t:"GRN1",c:"GRN"},{t:"VCC",c:"VCC"}],
          [{t:"BLU0",c:"BLU"},{t:"RED0",c:"RED"},{t:"GRN0",c:"GRN"},{t:"VCC",c:"VCC"},{t:"BLU1",c:"BLU"},{t:"RED1",c:"RED"},{t:"GRN1",c:"GRN"},{t:"VCC",c:"VCC"}]
        ]
      ]
    };
  },
  computed: {
    outputMode() {
      return this.settings ? this.settings.outputmode : 0;
    },
  },
  methods: {
    patchLEDType() {
        // Live patch LED type
        for (var c = 0; c < this.settings.stripconfig.length; c++) {
          if (this.stripTypes[this.settings.stripconfig[c].type].clock === 1 &&
              this.showConfigs[this.settings.outputmode].clock[c] === 0) {
              for (var d = 0; d <this.stripTypes.length; d++) {
                if (this.stripTypes[d].clock == 0) {
                  this.settings.stripconfig[c].type = this.stripTypes[d].value;
                  break;
                }
              }
          }
        }
    },
    patchRGBType() {
        // Live patch RGB type
        for (var c = 0; c < this.settings.rgbconfig.length; c++) {
          if (this.showConfigs[this.settings.outputmode].components[c] != 0 &&
              this.showConfigs[this.settings.outputmode].components[c] != this.rgbTypes[this.settings.rgbconfig[c].type].components) {
            for (var d = 0; d <this.rgbTypes.length; d++) {
              if (this.showConfigs[this.settings.outputmode].components[c] == this.rgbTypes[d].components) {
                this.settings.rgbconfig[c].type = this.rgbTypes[d].value;
                break;
              }
            }
          }
        }
    }
  },
  watch: {
    outputMode() {
      if (this.settings) {
        this.patchLEDType();
        this.patchRGBType();
      }
    }
  },
  mounted() {
    this.axios
      .get(this.baseURL + "status")
      .then(response => { this.status = response.data;
        this.axios
        .get(this.baseURL + "settings")
        .then(response => { this.settings = response.data })
        .finally(() => this.settingsloading = false);
      })
      .finally(() => this.statusloading = false)
  },
  components: {
    RGBConfig,
    StripConfig,
    OutputConfig,
    ActionBar,
    Status,
    PinLayout,
    NetworkInterface
  }
}; 
</script>

<style>
@import url(http://fonts.googleapis.com/css?family=Montserrat:400);
@import url(http://fonts.googleapis.com/css?family=Montserrat:600);
#app {
  font-family: "Montserrat", Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

body {
  background-color: #cccccc;
}
button, input, select, textarea {
  font-family : inherit;
  font-size   : 100%;
  font-weight : 600;
}

.active {
  background-color: red;
}

.checkbox {
	-webkit-appearance: none;
	background-color: #fafafa;
	border: 1px solid #cacece;
	box-shadow: 0 1px 2px rgba(0,0,0,0.05), inset 0px -15px 10px -12px rgba(0,0,0,0.05);
	padding: 9px;
	border-radius: 3px;
	display: inline-block;
	position: relative;
}

.checkbox:active, .regular-checkbox:checked:active {
	box-shadow: 0 1px 2px rgba(0,0,0,0.05), inset 0px 1px 3px rgba(0,0,0,0.1);
}

.checkbox:checked {
	background-color: #e9ecee;
	border: 1px solid #adb8c0;
	box-shadow: 0 1px 2px rgba(0,0,0,0.05), inset 0px -15px 10px -12px rgba(0,0,0,0.05), inset 15px 10px -12px rgba(255,255,255,0.1);
	color: #000000;
}

.checkbox:checked:after {
	content: '\2714';
	font-size: 14px;
	position: absolute;
	top: 0px;
	left: 3px;
	color: #000000;
}

</style>
