<template>
  <div class="strip">
    <div class="header">
      LED Strip ({{ terminalNames[stripIndex] }})
    </div>
    <div class="left">
      LED type
    </div>
    <div class="right">
      <select v-model="stripConfig().outputtype">
        <option
          v-for="stripType in stripOutputTypesFiltered(showConfigs[settings.outputconfig], stripIndex)" 
          :key="stripType.id"
          :value="stripType.value"
        >
          {{ stripType.text }}
        </option>
      </select>
    </div>
    <div class="left">
      Input Data Type {{ hasGlobIllum() }}
    </div>
    <div class="right">
      <select v-model="stripConfig().inputtype">
        <option
          v-for="stripInputTypeI in stripInputTypes" 
          :key="stripInputTypeI.id"
          :value="stripInputTypeI.value"
        >
          {{ stripInputTypeI.text }}
        </option>
      </select>
    </div>
    <div v-if="hasGlobIllum()">
      <div class="left">
        Global Illumination
      </div>
      <div class="right">
        <input
          v-model="stripConfig().globillum"
          class="smallnumber"
          :style="{ border : validateGlobIllum() ? '2px solid green' : '2px solid red' }"
        >
        <span class="footnote">
          %
        </span>
      </div>
    </div>
    <div class="left">
      Limit Brightness
    </div>
    <div class="right">
      <input
        v-model="stripConfig().complimit"
        class="smallnumber"
        :style="{ border : validateCompLimit() ? '2px solid green' : '2px solid red' }"
      >
      <span class="footnote">
        %
      </span>
    </div>
    <div class="left">
      Number of LEDs
    </div>
    <div class="right">
      <input
        v-model="stripConfig().length"
        class="smallnumber"
        :style="{ border : validateLEDs() ? '2px solid green' : '2px solid red' }"
      >
      <span
        v-if="!isMobile()"
        class="footnote"
      >
        Maximum: <span style="font-weight:600;">{{ maxLEDLength() }}</span>
      </span>
    </div>
    <div
      v-for="(item, itemIndex) in universesFiltered(stripConfig())"
      :key="item.id"
      class="universe"
    >
      <div
        v-if="itemIndex == 0"
        class="left_universe"
      >
        ArtNet Universe{{ (universesFiltered(stripConfig()).length == 1 ||
          universesFiltered(stripConfig()).length == 0 ) ? "" : "s" }}
      </div>
      <div
        v-else
        class="left_universe"
      />
      <div class="right_universe">
        <input
          v-model="item.artnet"
          class="smallnumber"
          :style="{ border : validateArtnetUniverse(itemIndex) ? '2px solid green' : '2px solid red' }"
        >
        <span
          v-if="!isMobile()"
          class="footnote"
        >
          DMX Channels: <span style="font-weight:600;">1 ... {{ universesDMXLength(itemIndex) }}</span>
        </span>
      </div>
    </div>
    <div
      v-for="(item, itemIndex) in universesFiltered(stripConfig())"
      :key="item.id"
      class="universe"
    >
      <div
        v-if="itemIndex == 0"
        class="left_universe"
      >
        E1.31 Universe{{ (universesFiltered(stripConfig()).length == 1 ||
          universesFiltered(stripConfig()).length == 0 ) ? "" : "s" }}
      </div>
      <div
        v-else
        class="left_universe"
      />
      <div class="right_universe">
        <input
          v-model="item.e131"
          class="smallnumber"
          :style="{ border : validateE131Universe(itemIndex) ? '2px solid green' : '2px solid red' }"
        >
        <span
          v-if="!isMobile()"
          class="footnote"
        >
          DMX Channels: <span style="font-weight:600;">1 ... {{ universesDMXLength(itemIndex) }}</span>
        </span>
      </div>
    </div>
    <div class="universe">
      <div class="left_color">
        Startup Color
      </div>
      <div class="right_color">
        <ColorSwatch
          :change="colorChange"
          :initial="initialColor"
        />
      </div>
    </div>
    <div class="left">
      Startup Mode
    </div>
    <div class="right">
      <select
        v-model="stripConfig().startupmode"
      >
        <option
          v-for="mode in startupModes"
          :key="mode.id" 
          :value="mode.value"
        >
          {{ mode.text }}
        </option>
      </select>
    </div>

    <div class="universe">
      <div
        v-if="compLength() >= 4"
        class="left_color"
      >
        Startup White
      </div>
      <div
        v-if="compLength() >= 4"
        class="right_universe"
      >
        <input
          v-model="stripConfig().color.a"
          class="smallnumber"
          :style="{ border : validateAlphaValue() ? '2px solid green' : '2px solid red' }"
        >
      </div>
    </div>
    <div
      class="spacer"
      style="clear: both;"
    />
  </div>
</template>

<script>
import ColorSwatch from "./ColorSwatch.vue";
export default {
  name: "StripConfig",
  components: {
    ColorSwatch
  },
  props: {
    stripIndex: {
      type:Number,
      default:0
    },
    startupModes: {
      type:Array,
      default: function() { return [] }
    },
    terminalNames: {
      type:Array,
      default: function() { return [] }
    },
    settings: {
      type:Object,
      default: function() { return {} }
    },
    stripOutputTypes: {
      type:Array,
      default: function() { return [] }
    },
    stripInputTypes: {
      type:Array,
      default: function() { return [] }
    },
    showConfigs: {
      type:Array,
      default: function() { return [] }
    },
    settingsInternal: {
      type:Object,
      default: function() { return {} }
    },
  },
  data() {
    return {
    }
  },
  computed: {
  },
  methods: {
      isMobile() {
        if(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
          return true
        } else {
          return false
        }
      },
      stripConfig() {
        return this.settings.stripconfig[this.stripIndex];
      },
      stripOutputType() {
        return this.stripOutputTypes[this.stripConfig().outputtype];
      },
      stripInputType() {
        return this.stripInputTypes[this.stripConfig().inputtype];
      },
      hasGlobIllum() {
        return this.stripOutputType().globillum;
      },
      initialColor() {
        return { 
          r: this.stripConfig().color.r, 
          g: this.stripConfig().color.g,  
          b: this.stripConfig().color.b
        };
      },
      checkInteger(value, min, max) {
        return (!isNaN(Number(value)) &&
                 parseInt(value) <= max &&
                 parseInt(value) >= min &&
                 Number(value) == parseInt(value));
      },
      checkFloat(value, min, max) {
        return (!isNaN(Number(value)) &&
                 parseFloat(value) <= max &&
                 parseFloat(value) >= min &&
                 Number(value) == parseFloat(value));
      },
      compLength() {
        return this.stripInputType().components;
      },
      validateAlphaValue() {
          var value = this.stripConfig().color.a;
          if (this.checkInteger(value, 0, 255)) {
            return true;
          } else {
            return false;
          }
      },
      validateCompLimit() {
        var length = this.stripConfig().complimit;
        return this.checkFloat(length, 0, 100);
      },
      validateGlobIllum() {
        var length = this.stripConfig().globillum;
        return this.checkFloat(length, 0, 100);
      },
      validateLEDs() {
        var length = this.stripConfig().length;
        var maxLength = this.maxLEDLength(this.stripConfig());
        return this.checkInteger(length, 1, maxLength);
      },
      validateArtnetUniverse(index) {
          var value = this.stripConfig().universes[index].artnet;
          if (this.checkInteger(value, 0, 32767)) {
            return true;
          } else {
            return false;
          }
      },
      validateE131Universe(index) {
          var value = this.stripConfig().universes[index].e131;
          if (this.checkInteger(value, 1, 63999)) {
            return true;
          } else {
            return false;
          }
      },
      colorChange(color) {
        this.stripConfig().color.r = color.r;
        this.stripConfig().color.g = color.g;
        this.stripConfig().color.b = color.b;
      },
      panelHeight() {
        return 180 + (Math.max(0,this.universesFiltered(this.stripConfig()).length-1)) * 32;
      },
      maxLEDLength() {
        var components = Math.max(this.stripOutputType().components * this.stripOutputType().bitslen, 
                                  this.stripInputType().components * this.stripInputType().bitslen) / 8;
        return (parseInt(512 / components)) * this.stripConfig().universes.length;
      },
      universesDMXLength(index) {
        var components = ( this.stripInputType().components * this.stripInputType().bitslen) / 8;
        var perUniverse = parseInt(512 / components);
        if ((parseInt(512 / components) * (index + 1)) <= this.stripConfig().length) {
          return perUniverse * components;
        }
        return (this.stripConfig().length % perUniverse) * components;
      },
      universesFiltered(stripconfig) {
        var components = (this.stripOutputTypes[stripconfig.outputtype].components *
                          this.stripOutputTypes[stripconfig.outputtype].bitslen ) / 8;
        function filterUniverses(universe, index) {
          if ((parseInt(512 / components) * (index + 0)) < stripconfig.length) {
            return true;
          }
          return false;
        }
        return stripconfig.universes.filter(filterUniverses);
      },
      stripOutputTypesFiltered(showConfig, index) {
          function filterStripType(stripType) {
            if (showConfig.clock[index] == 0 &&
                stripType.clock === 1) {
              return false;
            }
            return true;
          }
          return this.stripOutputTypes.filter(filterStripType);
      }
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
