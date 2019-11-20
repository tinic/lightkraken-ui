<template>
  <div class="strip">
    <div class="header">LED Strip ({{ terminalNames[stripIndex] }})</div>
    <div class="left">LED type</div>
    <div class="right">
    <select v-model="settings.stripconfig[stripIndex].outputtype">
      <option v-for="stripType in stripTypesFiltered(showConfigs[settings.outputmode], stripIndex)" 
              v-bind:value="stripType.value" v-bind:key="stripType.id" >{{ stripType.text }}
      </option>
    </select>
    </div>
		<div class="left">Number of LEDs</div>
		<div class="right">
			<input class="smallnumber" v-bind:style="{ border : validateLEDs() ? '2px solid green' : '2px solid red' }" v-model="settings.stripconfig[stripIndex].length">
      <span v-if="!isMobile()" class="footnote">
        Maximum: <span style="font-weight:600;">{{ maxLEDLength(settings.stripconfig[stripIndex]) }}</span>
      </span>
		</div>
    <div class="universe" v-for="(item, itemIndex) in universesFiltered(settings.stripconfig[stripIndex])" v-bind:key="item.id">
      <div v-if="itemIndex == 0" class="left_universe">ArtNet Universe{{ (universesFiltered(settings.stripconfig[stripIndex]).length == 1 ||
                                                                       universesFiltered(settings.stripconfig[stripIndex]).length == 0 ) ? "" : "s" }}</div>
      <div v-else class="left_universe"></div>
      <div class="right_universe">
        <input class="smallnumber" v-bind:style="{ border : validateArtnetUniverse(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="item.artnet">
        <span v-if="!isMobile()" class="footnote">
            DMX Channels: <span style="font-weight:600;">1 ... {{ universesDMXLength(settings.stripconfig[stripIndex], itemIndex) }}</span>
        </span>
      </div>
		</div>
    <div class="universe" v-for="(item, itemIndex) in universesFiltered(settings.stripconfig[stripIndex])" v-bind:key="item.id">
      <div v-if="itemIndex == 0" class="left_universe">E1.31 Universe{{ (universesFiltered(settings.stripconfig[stripIndex]).length == 1 ||
                                                                       universesFiltered(settings.stripconfig[stripIndex]).length == 0 ) ? "" : "s" }}</div>
      <div v-else class="left_universe"></div>
      <div class="right_universe">
        <input class="smallnumber" v-bind:style="{ border : validateE131Universe(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="item.e131">
        <span v-if="!isMobile()" class="footnote">
            DMX Channels: <span style="font-weight:600;">1 ... {{ universesDMXLength(settings.stripconfig[stripIndex], itemIndex) }}</span>
        </span>
      </div>
		</div>
    <div class="universe">
      <div class="left_color">Startup Color</div>
      <div class="right_color"><ColorSwatch v-bind:change="colorChange" v-bind:initial="initialColor"/></div>
    </div>
    <div class="universe">
      <div v-if="compLength() >= 4" class="left_color">Startup White</div>
      <div v-if="compLength() >= 4" class="right_universe">
        <input class="smallnumber" v-bind:style="{ border : validateAlphaValue() ? '2px solid green' : '2px solid red' }" v-model="settings.stripconfig[stripIndex].color.a">
      </div>
    </div>
    <div class="spacer" style="clear: both;"></div>
  </div>
</template>

<script>
import ColorSwatch from "./ColorSwatch.vue";
export default {
  name: "StripConfig",
  data() {
    return {
    }
  },
  methods: {
      isMobile() {
        if(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
          return true
        } else {
          return false
        }
      },
      initialColor() {
        return { 
          r: this.settings.stripconfig[this.stripIndex].color.r, 
          g: this.settings.stripconfig[this.stripIndex].color.g,  
          b: this.settings.stripconfig[this.stripIndex].color.b
        };
      },
      checkInteger(value, min, max) {
        return (!isNaN(Number(value)) &&
                 parseInt(value) <= max &&
                 parseInt(value) >= min &&
                 Number(value) == parseInt(value));
      },
      compLength() {
        return this.stripTypes[this.settings.stripconfig[this.stripIndex].outputtype].components;
      },
      validateAlphaValue() {
          var value = this.settings.stripconfig[this.stripIndex].color.a;
          if (this.checkInteger(value, 0, 255)) {
            return true;
          } else {
            return false;
          }
      },
      validateLEDs() {
        var length = this.settings.stripconfig[this.stripIndex].length;
        var maxLength = this.maxLEDLength(this.settings.stripconfig[this.stripIndex]);
        return this.checkInteger(length, 1, maxLength);
      },
      validateArtnetUniverse(index) {
          var value = this.settings.stripconfig[this.stripIndex].universes[index].artnet;
          if (this.checkInteger(value, 0, 32767)) {
            return true;
          } else {
            return false;
          }
      },
      validateE131Universe(index) {
          var value = this.settings.stripconfig[this.stripIndex].universes[index].e131;
          if (this.checkInteger(value, 1, 63999)) {
            return true;
          } else {
            return false;
          }
      },
      colorChange(color) {
        this.settings.stripconfig[this.stripIndex].color.r = color.r;
        this.settings.stripconfig[this.stripIndex].color.g = color.g;
        this.settings.stripconfig[this.stripIndex].color.b = color.b;
      },
      panelHeight() {
        return 180 + (Math.max(0,this.universesFiltered(this.settings.stripconfig[this.stripIndex]).length-1)) * 32;
      },
      maxLEDLength(stripconfig) {
        var components = this.stripTypes[stripconfig.outputtype].components;
        return (parseInt(512 / components)) * this.settings.stripconfig[this.stripIndex].universes.length;
      },
      universesDMXLength(stripconfig, index) {
        var components = this.stripTypes[stripconfig.outputtype].components;
        var perUniverse = parseInt(512 / components);
        if ((parseInt(512 / components) * (index + 1)) <= stripconfig.length) {
          return perUniverse * components;
        }
        return (stripconfig.length % perUniverse) * components;
      },
      universesFiltered(stripconfig) {
        var components = this.stripTypes[stripconfig.outputtype].components;
        function filterUniverses(universe, index) {
          if ((parseInt(512 / components) * (index + 0)) < stripconfig.length) {
            return true;
          }
          return false;
        }
        return stripconfig.universes.filter(filterUniverses);
      },
      stripTypesFiltered(showConfig, index) {
          function filterStripType(stripType) {
            if (showConfig.clock[index] == 0 &&
                stripType.clock === 1) {
              return false;
            }
            return true;
          }
          return this.stripTypes.filter(filterStripType);
      }
  },
  computed: {
  },
  props: {
    stripIndex: Number,
    terminalNames: Array,
    settings: Object,
    stripTypes: Array,
    showConfigs: Array,
    settingsInternal: Object
  },
  components: {
    ColorSwatch
  }
};
</script>

<style scoped>
*.strip {
  max-width: 760px;
  background-color: #eeeeee;
  padding: 10px;
  margin: 10px;
  border-radius: 16px;
}

*.header {
  height: 40px;
  font-size: large;
  font-weight: 600;
}

*.left {
  clear: left;
  float: left;
  width: 40%;
  margin: 2px;
  margin-top: 5px;
  text-align: right;
}

*.right {
  float: left;
  overflow: auto;
  width: 50%;
  margin: 2px;
  padding-left: 10px;
  min-width: 100px;
  max-width: 400px;
  text-align: left;
}

*.left_color {
  clear: left;
  float: left;
  width: 40%;
  margin: 2px;
  margin-top: 4px;
  text-align: right;
  padding-top: 6px;
  padding-bottom: 6px;
}

*.right_color {
  float: left;
  overflow: auto;
  width: 50%;
  margin: 2px;
  margin-top: 4px;
  padding-left: 10px;
  text-align: left;
}

*.left_universe {
  clear: left;
  float: left;
  width: 40%;
  margin: 2px;
  margin-top: 4px;
  text-align: right;
}

*.right_universe {
  float: left;
  width: 50%;
  margin: 2px;
  padding-left: 10px;
  min-width: 100px;
  max-width: 400px;
  text-align: left;
}

*.universeIndex {
  float: left;
  width: 8px;
  text-align: center;
  padding: 2px;
}

*.footnote {
  padding-left: 10px;
  font-size: 80%;
}

input.smallnumber {
    width:60px;
    margin-bottom: 4px;
}

</style>
