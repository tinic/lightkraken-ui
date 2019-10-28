<template>
  <div class="strip" v-bind:style="{ height: panelHeight() + 'px' }">
    <div class="header">LED Strip ({{ terminalNames[stripIndex] }})</div>
    <div class="left">LED type</div>
    <div class="right">
    <select v-model="settings.stripconfig[stripIndex].type">
      <option v-for="stripType in stripTypesFiltered(showConfigs[settings.outputmode], stripIndex)" 
              v-bind:value="stripType.value" v-bind:key="stripType.id" >{{ stripType.text }}
      </option>
    </select>
    </div>
		<div class="left">Number of LEDs</div>
		<div class="right">
			<input class="smallnumber" v-bind:style="{ border : validateLEDs() ? '2px solid green' : '2px solid red' }" v-model="settings.stripconfig[stripIndex].length">
      <span class="footnote">
        Maximum: <b>{{ maxLEDLength(settings.stripconfig[stripIndex]) }}</b>
      </span>
		</div>
		<div class="left_universe">DMX Universe{{ (universesFiltered(settings.stripconfig[stripIndex]).length == 1 ||
                                  universesFiltered(settings.stripconfig[stripIndex]).length == 0 ) ? "" : "s" }}</div>
		<div class="right_universe">
      <div class="universe" v-for="(item, itemIndex) in universesFiltered(settings.stripconfig[stripIndex])" v-bind:key="item.id">
        <input class="smallnumber" v-bind:style="{ border : validateUniverse(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="item.universe">
        <span class="footnote">
           DMX Channels: <b>1 ... {{ universesDMXLength(settings.stripconfig[stripIndex], itemIndex) }}</b>
        </span>
        <br>
      </div>
		</div>
    <div class="left_color">Startup Color</div>
    <div class="right_color"><ColorSwatch v-bind:change="colorChange"/></div>
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
      checkInteger(value, min, max) {
        return (!isNaN(Number(value)) &&
                 parseInt(value) <= max &&
                 parseInt(value) >= min &&
                 Number(value) == parseInt(value));
      },
      validateLEDs() {
        var length = this.settings.stripconfig[this.stripIndex].length;
        var maxLength = this.maxLEDLength(this.settings.stripconfig[this.stripIndex]);
        return this.checkInteger(length, 1, maxLength);
      },
      validateUniverse(index) {
          var value = this.settings.stripconfig[this.stripIndex].universes[index].universe;
          if (this.checkInteger(value, 0, 32767)) {
            return true;
          } else {
            return false;
          }
      },
      colorChange(color) {
        color;
      },
      panelHeight() {
        return 180 + (Math.max(0,this.universesFiltered(this.settings.stripconfig[this.stripIndex]).length-1)) * 32;
      },
      maxLEDLength(stripconfig) {
        var components = this.stripTypes[stripconfig.type].components;
        return (parseInt(512 / components)) * 6;
      },
      universesDMXLength(stripconfig, index) {
        var components = this.stripTypes[stripconfig.type].components;
        var perUniverse = parseInt(512 / components);
        if ((parseInt(512 / components) * (index + 1)) <= stripconfig.length) {
          return perUniverse * components;
        }
        return (stripconfig.length % perUniverse) * components;
      },
      universesFiltered(stripconfig) {
        var components = this.stripTypes[stripconfig.type].components;
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
  width: 729px;
  height: 300px;
  background-color: #eeeeee;
  padding: 10px;
  margin: 10px;
  border-radius: 16px;
}

*.header {
  height: 40px;
  font-size: large;
  font-weight: bold;
}

*.left {
  clear: left;
  float: left;
  width: 40%;
  margin: 0 0 0.6em;
  padding: 2px;
  text-align: right;
}

*.right {
  float: left;
  overflow: auto;
  width: 50%;
  margin: 0 0 0.6em 0.4em;
  padding-left: 0.6em;
  text-align: left;
}

*.left_color {
  clear: left;
  float: left;
  width: 40%;
  margin: 0 0 0.6em;
  padding: 2px;
  text-align: right;
  padding-top: 6px;
  padding-bottom: 6px;
}

*.right_color {
  float: left;
  overflow: auto;
  width: 50%;
  margin: 0 0 0.6em 0.4em;
  padding-left: 0.6em;
  text-align: left;
}

*.left_universe {
  clear: left;
  float: left;
  width: 40%;
  margin: 0 0 0.6em;
  padding: 2px;
  text-align: right;
}


*.right_universe {
  float: left;
  overflow: auto;
  width: 400px;
  margin: 0 0 0.6em 0.4em;
  padding-left: 0.6em;
  text-align: left;
}

*.universe {
  float: left;
  height: 32px;
  width: 380px;
  padding-right: 10px;
}

*.universeIndex {
  float: left;
  width: 8px;
  text-align: center;
  padding-top: 3px;
  padding-right: 10px;
}

*.footnote {
  padding-left: 140px;
  font-size: 80%;
}

input.smallnumber {
    width:60px;
}

</style>