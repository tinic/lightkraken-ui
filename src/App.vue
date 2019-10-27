<template>
  <div id="app">
    <div v-if="this.statusloading === false">
      <Status
        v-bind:status="status"/>
    </div>
    <div v-if="this.settingsloading === false">
      <PinLayout
        v-bind:stripTypes="stripTypes"
        v-bind:settings="settings"
        v-bind:pinTable="pinTable"/>
      <NetworkInterface
        v-bind:settings="settings"
        v-bind:settingsInternal="settingsInternal"/>
      <OutputConfig
        v-bind:outputModes="outputModes"
        v-bind:settings="settings"
        v-bind:settingsInternal="settingsInternal"/>
      <StripConfig
        v-if="this.showConfigs[this.settings.outputmode].strip[0] === true"
        v-bind:strip=0
        v-bind:stripTypes="stripTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"
        v-bind:settingsInternal="settingsInternal"/>
      <StripConfig 
        v-if="this.showConfigs[this.settings.outputmode].strip[1] === true"
        v-bind:strip=1
        v-bind:stripTypes="stripTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"
        v-bind:settingsInternal="settingsInternal"/>
      <RGBConfig
        v-if="this.showConfigs[this.settings.outputmode].rgb[0] === true"
        v-bind:strip=0
        v-bind:settings="settings"
        v-bind:settingsInternal="settingsInternal"/>
      <RGBConfig 
        v-if="this.showConfigs[this.settings.outputmode].rgb[1] === true"
        v-bind:strip=1
        v-bind:settings="settings"
        v-bind:settingsInternal="settingsInternal"/>
      <ActionBar
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
      status: null,
      statusloading: true,
      settings: null,
      settingsloading: true,
      settingsInternal: {
        ipv4addressInvalid : Boolean,
        ipv4netmaskInvalid : Boolean,
        ipv4gatewayInvalid : Boolean
      },
      outputModes: [
          { text: '2 x RGB Strip', value : 0 },
          { text: '1 x RGB Strip + 1 x RGB Analog', value : 1 },
          { text: '2 x RGB Strip + 1 x RGB Analog', value : 2 },
          { text: '1 x RGB Strip + 1 x RGB+W Analog', value : 3 },
          { text: '2 x RGB Analog', value : 4 },
      ],
      showConfigs: [
          { strip: [true,  true ], rgb : [false, false], clock:1 },
          { strip: [false, true ], rgb : [true,  false], clock:1 },
          { strip: [true,  true ], rgb : [true,  false], clock:0 },
          { strip: [false, true ], rgb : [true,  false], clock:0 },
          { strip: [false, false], rgb : [true,  true ], clock:0 }
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
  mounted() {
    this.axios
      .get("https://my-json-server.typicode.com/tinic/lightguy-ui/status")
      .then(response => { this.status = response.data })
      .finally(() => this.statusloading = false)
    this.axios
      .get("https://my-json-server.typicode.com/tinic/lightguy-ui/settings")
      .then(response => { this.settings = response.data })
      .finally(() => this.settingsloading = false)
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
