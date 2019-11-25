<template>
  <div id="app">
    <div v-if="this.loadingSomething === true">
    </div>
    <div v-if="this.savingSomething === true">
    </div>
    <div v-if="this.statusLoading === false">
      <Status
        v-bind:status="status"/>
    </div>
    <div v-if="this.settingsLoading === false">
      <PinLayout
        v-bind:terminalNames="terminalNames"
        v-bind:stripOutputTypes="stripOutputTypes"
        v-bind:settings="settings"
        v-bind:pinTable="pinTable"/>
      <NetworkInterface
        v-bind:settings="settings"/>
      <OutputConfig
        v-bind:outputModes="outputModes"
        v-bind:settings="settings"/>
      <StripConfig
        v-if="this.showConfigs[this.settings.outputconfig].strip[0] === true"
        v-bind:stripIndex=0
        v-bind:terminalNames="terminalNames"
        v-bind:stripOutputTypes="stripOutputTypes"
        v-bind:stripInputTypes="stripInputTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <RGBConfig
        v-if="this.showConfigs[this.settings.outputconfig].rgb[0] === true"
        v-bind:rgbIndex=0
        v-bind:terminalNames="terminalNames"
        v-bind:rgbInputTypes="rgbInputTypes"
        v-bind:rgbOutputTypes="rgbOutputTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <StripConfig 
        v-if="this.showConfigs[this.settings.outputconfig].strip[1] === true"
        v-bind:stripIndex=1
        v-bind:terminalNames="terminalNames"
        v-bind:stripOutputTypes="stripOutputTypes"
        v-bind:stripInputTypes="stripInputTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <RGBConfig 
        v-if="this.showConfigs[this.settings.outputconfig].rgb[1] === true"
        v-bind:rgbIndex=1
        v-bind:terminalNames="terminalNames"
        v-bind:rgbInputTypes="rgbInputTypes"
        v-bind:rgbOutputTypes="rgbOutputTypes"
        v-bind:showConfigs="showConfigs"
        v-bind:settings="settings"/>
      <ActionBar
        v-bind:savingSomething="savingSomething"
        v-bind:baseURL="baseURL"
        v-bind:settings="settings"/>
      <div class="credit">Lightkraken was designed and built<br>by Tinic Uro in 2019</div>
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
      loadingSomething: false,
      savingSomething: false,
      status: null,
      statusLoading: true,
      settings: null,
      settingsLoading: true,
      terminalNames: [
         "Terminal A",
         "Terminal B",
      ],
      outputModes: [
          { text: '2 x RGB Strip', value : 0, strips : 2 },
          { text: '1 x RGB Strip + 1 x Analog RGB', value : 1, strips : 1 },
          { text: '2 x RGB Strip + 1 x Analog RGB', value : 2, strips : 2 },
          { text: '1 x RGB Strip + 1 x Analog RGB+W', value : 3, strips : 1 },
          { text: '2 x Analog RGB', value : 4, strips : 0 },
      ],
      showConfigs: [
          { strip: [true,  true ], rgb : [false, false], clock:[1, 1], components:[0, 0] },
          { strip: [false, true ], rgb : [true,  false], clock:[0, 1], components:[3, 0] },
          { strip: [true,  true ], rgb : [true,  false], clock:[0, 0], components:[3, 0] },
          { strip: [false, true ], rgb : [true,  false], clock:[0, 0], components:[4, 0] },
          { strip: [false, false], rgb : [true,  true ], clock:[0, 0], components:[3, 3] }
      ],
      stripInputTypes: [
          { text: 'RGB (8-bit, Device)', value : 0, components: 3, size:8 },
          { text: 'RGBW (8-bit, Device)', value : 1, components: 4, size:8 },
          { text: 'RGB (8-bit, sRGB)', value : 2, components: 3, size:8 },
          { text: 'RGBW (8-bit, sRGB)', value : 3, components: 4, size:8 }
      ],
      stripOutputTypes: [
          { text: 'WS2812 (RGB)',  value : 0,  clock : 0, components: 3, globillum: false },
          { text: 'SK6812 (RGB)',  value : 1,  clock : 0, components: 3, globillum: false },
          { text: 'TM1804 (RGB)',  value : 2,  clock : 0, components: 3, globillum: false },
          { text: 'UCS1904 (RGB)', value : 3,  clock : 0, components: 3, globillum: false },
          { text: 'GS8208 (RGB)',  value : 4,  clock : 0, components: 3, globillum: false },
          { text: 'SK6812 (RGBW)', value : 5,  clock : 0, components: 4, globillum: false },
          { text: 'APA102 (RGB)',  value : 6,  clock : 1, components: 3, globillum: true  },
          { text: 'APA107 (RGB)',  value : 7,  clock : 1, components: 3, globillum: true  },
          { text: 'P9813 (RGB)',   value : 8,  clock : 1, components: 3, globillum: false },
          { text: 'SK9822 (RGB)',  value : 9,  clock : 1, components: 3, globillum: true  },
          { text: 'HDS107S (RGB)', value : 10, clock : 1, components: 3, globillum: false },
          { text: 'LPD8806 (RGB)', value : 11, clock : 0, components: 3, globillum: false },
          { text: 'TLS3001 (RGB)', value : 12, clock : 0, components: 3, globillum: false },
          { text: 'TM1829 (RGB)',  value : 13, clock : 0, components: 3, globillum: false }
      ],
      rgbInputTypes: [
          { text: 'RGB (8-bit, Device)',  value : 0,  components: 3, size:8  },
          { text: 'RGBW (8-bit, Device)',  value : 1,  components: 4, size:8  },
          { text: 'RGBWW (8-bit, Device)',  value : 2,  components: 5, size:8  },
          { text: 'RGB (8-bit, sRGB)',  value : 3,  components: 3, size:8  },
          { text: 'RGBW (8-bit, sRGB)',  value : 4,  components: 4, size:8  },
          { text: 'RGBWW (8-bit, sRGB)',  value : 5,  components: 5, size:8  },
      ],
      rgbOutputTypes: [
          { text: 'RGB (14-bit)',  value : 0,  components: 3, size:8  },
          { text: 'RGBW (14-bit)',  value : 1,  components: 4, size:8  },
          { text: 'RGBWW (14-bit)',  value : 2,  components: 5, size:8  },
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
    baseURL() {
      if(process.env.NODE_ENV === "development") {
        return 'http://my-json-server.typicode.com/tinic/lightkraken-ui/';
      }
      if(process.env.NODE_ENV === "test") {
        return 'http://lightkraken-09dfe272/';
      }
      return '/';
    }
  },
  methods: {
    load() {
      this.loadingSomething = true;
      this.axios
        .get(this.baseURL + "status")
        .then(response => { this.status = response.data;
          this.loadingSomething = true;
          this.axios
          .get(this.baseURL + "settings")
          .then(response => { this.settings = response.data })
          .finally(() => {
            console.log(this.settings);
            this.settingsLoading = false;
            this.loadingSomething = false;
          });
        })
        .finally(() => {
          this.statusLoading = false;
          this.loadingSomething = false;
        })
    },
    patchLEDType() {
        // Live patch LED type
        for (var c = 0; c < this.settings.stripconfig.length; c++) {
          if (this.stripOutputTypes[this.settings.stripconfig[c].type].clock === 1 &&
              this.showConfigs[this.settings.outputmode].clock[c] === 0) {
              for (var d = 0; d <this.stripOutputTypes.length; d++) {
                if (this.stripOutputTypes[d].clock == 0) {
                  this.settings.stripconfig[c].type = this.stripOutputTypes[d].value;
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
    this.load();
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
@import url(http://fonts.googleapis.com/css?family=Montserrat:900);
#app {
  font-family: "Montserrat", Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  display: inline-block;
}

body {
  background-color: #d4d4d4;
}

button, input, select, textarea {
  font-family : inherit;
  font-size   : 100%;
  font-weight : 600;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.active {
  background-color: red;
}

input[type=checkbox]
{
  /* Double-sized Checkboxes */
  -ms-transform: scale(2); /* IE */
  -moz-transform: scale(2); /* FF */
  -webkit-transform: scale(2); /* Safari and Chrome */
  -o-transform: scale(2); /* Opera */
  transform: scale(2);
  margin: 7px;
}

.title {
  font-weight: 600;
  font-size: 200%;
  padding: 10px;
  margin: 10px;
  width: 729px;
}

.neon {
    max-width: 760px;
    position: relative;
    overflow: hidden;
    filter: brightness(220%);
    margin: 10px;
    padding: 10px;
    border-radius: 16px;
}

.text {
    color: white;
    font-size: 40px;
    font-weight: 900;
    position: relative;
    user-select: none;
}

.text::before {
    content: attr(data-text);
    position: absolute;
    color: white;
    filter: blur(0.02em);
    mix-blend-mode: difference;
}

.gradient {
    position: absolute;
    background: linear-gradient(45deg, red, gold, green, blue, red);
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    mix-blend-mode: multiply;
}

.spotlight {
    position: absolute;
    top: -100%;
    left: -140%;
    right: 0;
    bottom: 0;
    background: 
        radial-gradient(
            circle,
            white,
            transparent 50%
        ) center / 25% 25%,
        radial-gradient(
            circle,
            white,
            black 25%
        ) center / 12.5% 12.5%;
    mix-blend-mode: color-dodge;
}

.credit {
  display: inline-block;
  font-size:80%;
  color: #555555;
  padding-top: 20px;
}

</style>
