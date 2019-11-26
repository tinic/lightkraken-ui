<template>
  <div class="rgb">
    <div class="header">Analog RGB ({{ terminalNames[rgbIndex] }})</div>
    <div class="left">Input Data Type</div>
    <div class="right">
    <select v-model="rgbConfig().inputtype">
      <option v-for="rgbInputType in rgbInputTypes" 
              v-bind:value="rgbInputType.value" v-bind:key="rgbInputType.id" >{{ rgbInputType.text }}
      </option>
    </select>
    </div>
    <div class="left">Output Type</div>
    <div class="right">
    <select v-model="rgbConfig().outputtype">
      <option v-for="rgbOutputType in rgbOutputTypeFiltered(showConfigs[settings.outputconfig], rgbIndex)" 
              v-bind:value="rgbOutputType.value" v-bind:key="rgbOutputType.id" >{{ rgbOutputType.text }}
      </option>
    </select>
    </div>
		<div class="left">Limit Brightness</div>
		<div class="right">
			<input class="smallnumber" v-bind:style="{ border : validatePwmLimit() ? '2px solid green' : '2px solid red' }" v-model="rgbConfig().pwmlimit">
      <span class="footnote">
        %
      </span>
		</div>
    <div class="left_universe">ArtNet Mapping</div>
    <div class="right_universe">
      <div class="universefield" style="font-size: 80%; padding-top:2px;">Universe</div>
      <div class="channelfield" style="font-size: 80%; padding-top:2px;">Channel</div>
    </div>
    <div v-for="(item, itemIndex) in componentsSplice()" v-bind:key="item.id">
      <div class="left_universe">{{ item.text }}</div>
      <div class="right_universe">
        <input class="universefield" v-bind:style="{ border : validateArtnetUniverse(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="rgbComponents(itemIndex).artnet.universe">
        <input class="channelfield" v-bind:style="{ border : validateArtnetDMXChannel(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="rgbComponents(itemIndex).artnet.channel">
      </div>
    </div>
    <div class="left_universe">E1.31 Mapping</div>
    <div class="right_universe">
      <div class="universefield" style="font-size: 80%; padding-top:2px;">Universe</div>
      <div class="channelfield" style="font-size: 80%; padding-top:2px;">Channel</div>
    </div>
    <div v-for="(item, itemIndex) in componentsSplice()" v-bind:key="item.id">
      <div class="left_universe">{{ item.text }}</div>
      <div class="right_universe">
        <input class="universefield" v-bind:style="{ border : validateE131Universe(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="rgbComponents(itemIndex).e131.universe">
        <input class="channelfield" v-bind:style="{ border : validateE131DMXChannel(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="rgbComponents(itemIndex).e131.channel">
      </div>
    </div>
    <div class="left_color">Startup Color</div>
    <div class="right_color"><ColorSwatch v-bind:change="colorChange" v-bind:initial="initialColor"/></div>
    <div v-if="compLength() >= 4" class="left_color">Startup White</div>
    <div v-if="compLength() >= 4" class="right_universe">
      <input class="universefield" v-bind:style="{ border : validateComponentValue(3) ? '2px solid green' : '2px solid red' }" v-model="rgbComponents(3).value">
    </div>
    <div v-if="compLength() >= 5" class="left_color">Startup Warm White</div>
    <div v-if="compLength() >= 5" class="right_universe">
      <input class="universefield" v-bind:style="{ border : validateComponentValue(4) ? '2px solid green' : '2px solid red' }" v-model="rgbComponents(4).value">
    </div>
    <div class="spacer" style="clear: both;"></div>
  </div>
</template>

<script>
import ColorSwatch from "./ColorSwatch.vue";
export default {
  name: "RGBConfig",
  props: {
    rgbIndex: Number,
    terminalNames: Array,
    rgbInputTypes: Array,
    rgbOutputTypes: Array,
    settings: Object,
    showConfigs: Array,
    settingsInternal: Object
  },
  data() {
    return {
      componentTypes: [
          { text: 'Red', components : 3 },
          { text: 'Green', components : 3 },
          { text: 'Blue', components : 3 },
          { text: 'White', components : 4 },
          { text: 'Warm White', components : 5 }
      ],
    };
  },
  methods: {
      isMobile() {
        if(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
          return true
        } else {
          return false
        }
      },
      rgbConfig() {
        return this.settings.rgbconfig[this.rgbIndex];
      },
      rgbInputType() {
        return this.rgbInputTypes[this.rgbConfig().inputtype];
      },
      rgbComponents(index) {
        return this.rgbConfig().components[index];
      },
      compLength() {
        return this.rgbInputType().components;
      },
      dmxLength() {
        return this.rgbInputType().size == 16 ? 2 : 1;
      },
      dmxIndex(itemIndex) {
        return parseInt(this.rgbComponents(itemIndex).offset) + 1;
      },
      colorChange(color) {
        this.rgbComponents(0).value = color.r;
        this.rgbComponents(1).value = color.g;
        this.rgbComponents(2).value = color.b;
      },
      initialColor() {
        return { r: this.rgbComponents(0).value, 
                 g: this.rgbComponents(1).value, 
                 b: this.rgbComponents(2).value};
      },
      validateComponent(index) {
         index;
          return true;
      },
      checkFloat(value, min, max) {
        return (!isNaN(Number(value)) &&
                 parseFloat(value) <= max &&
                 parseFloat(value) >= min &&
                 Number(value) == parseFloat(value));
      },
      checkInteger(value, min, max) {
        return (!isNaN(Number(value)) &&
                 parseInt(value) <= max &&
                 parseInt(value) >= min &&
                 Number(value) == parseInt(value));
      },
      validatePwmLimit() {
        var pwm = this.rgbConfig().pwmlimit;
        return this.checkFloat(pwm, 0, 100);
      },
      validateArtnetUniverse(index) {
          var value = this.rgbComponents(index).artnet.universe;
          if (this.checkInteger(value, 0, 32767)) {
            return true;
          } else {
            return false;
          }
      },
      validateE131Universe(index) {
          var value = this.rgbComponents(index).e131.universe;
          if (this.checkInteger(value, 1, 63999)) {
            return true;
          } else {
            return false;
          }
      },
      validateComponentValue(index) {
          var value = this.rgbComponents(index).value;
          if (this.checkInteger(value, 0, 255)) {
            return true;
          } else {
            return false;
          }
      },
      validateArtnetDMXChannel(index) {
          var value = this.rgbComponents(index).artnet.channel;
          if (this.checkInteger(value, 1, 512)) {
            return true;
          } else {
            return false;
          }
      },
      validateE131DMXChannel(index) {
          var value = this.rgbComponents(index).e131.channel;
          if (this.checkInteger(value, 1, 512)) {
            return true;
          } else {
            return false;
          }
      },
      componentsSplice() {
          var components = this.rgbInputType().components;
          function filterComponentType(componentType) {
            if (components >= componentType.components) {
              return true;
            }
            return false;
          }
          return this.componentTypes.filter(filterComponentType);
      },
      rgbOutputTypeFiltered(showConfig, index) {
          function filterRGBType(rgbType) {
            if (showConfig.components[index] < rgbType.components) {
              return false;
            }
            return true;
          }
          return this.rgbOutputTypes.filter(filterRGBType);
      }
  },
  components: {
    ColorSwatch
  }
};
</script>

<style scoped>
*.rgb {
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

*.universefield {
  float: left;
  position: relative;
  left: 0px;
  width: 60px;
}

*.channelfield {
  float: left;
  position: relative;
  left: 10px;
  width: 40px;
}

*.left {
  clear: left;
  float: left;
  width: 40%;
  margin: 2px;
  margin-top: 4px;
  text-align: right;
}

*.right {
  float: left;
  overflow: auto;
  width: 50%;
  margin: 2px;
  margin-top: 4px;
  padding-left: 10px;
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
  padding-top: 2px;
  text-align: right;
}

*.right_universe {
  float: left;
  overflow: auto;
  width: 50%;
  padding-left: 10px;
  min-width: 100px;
  max-width: 400px;
  margin: 2px;
  margin-top: 4px;
  text-align: left;
}

*.footnote {
  font-size: 80%;
  position: relative;
  left: 28px;
  top: 2px;
}

input.smallnumber {
  float: left;
    width:60px;
}

</style>
