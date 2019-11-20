<template>
  <div class="rgb">
    <div class="header">Analog RGB ({{ terminalNames[rgbIndex] }})</div>
    <div class="left">RGB type</div>
    <div class="right">
    <select v-model="settings.rgbconfig[rgbIndex].outputtype">
      <option v-for="rgbType in rgbTypeFiltered(showConfigs[settings.outputconfig], rgbIndex)" 
              v-bind:value="rgbType.value" v-bind:key="rgbType.id" >{{ rgbType.text }}
      </option>
    </select>
    </div>
    <div class="left_universe">ArtNet Mapping</div>
    <div class="right_universe">
      <div class="universefield" style="font-size: 80%; padding-top:2px;">Universe</div>
      <div class="offsetfield" style="font-size: 80%; padding-top:2px;">Offset</div>
    </div>
    <div v-for="(item, itemIndex) in componentsSplice()" v-bind:key="item.id">
      <div class="left_universe">{{ item.text }}</div>
      <div class="right_universe">
        <input class="universefield" v-bind:style="{ border : validateArtnetUniverse(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[itemIndex].artnet">
        <input class="offsetfield" v-bind:style="{ border : validateChannelOffset(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[itemIndex].offset">
        <span v-if="dmxLength() == 2 && !isMobile()" class="footnote">
            DMX Channels: <span style="font-weight:600;">{{ dmxIndex(itemIndex) }} ... {{ dmxIndex(itemIndex) + 1 }}</span>
        </span>
        <span v-if="dmxLength() == 1 && !isMobile()" class="footnote">
            DMX Channel: <span style="font-weight:600;">{{ dmxIndex(itemIndex) }}</span>
        </span>
      </div>
    </div>
    <div class="left_universe">E1.31 Mapping</div>
    <div class="right_universe">
      <div class="universefield" style="font-size: 80%; padding-top:2px;">Universe</div>
      <div class="offsetfield" style="font-size: 80%; padding-top:2px;">Offset</div>
    </div>
    <div v-for="(item, itemIndex) in componentsSplice()" v-bind:key="item.id">
      <div class="left_universe">{{ item.text }}</div>
      <div class="right_universe">
        <input class="universefield" v-bind:style="{ border : validateE131Universe(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[itemIndex].e131">
        <input class="offsetfield" v-bind:style="{ border : validateChannelOffset(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[itemIndex].offset">
        <span v-if="dmxLength() == 2 && !isMobile()" class="footnote">
            DMX Channels: <span style="font-weight:600;">{{ dmxIndex(itemIndex) }} ... {{ dmxIndex(itemIndex) + 1 }}</span>
        </span>
        <span v-if="dmxLength() == 1 && !isMobile()" class="footnote">
            DMX Channel: <span style="font-weight:600;">{{ dmxIndex(itemIndex) }}</span>
        </span>
      </div>
    </div>
    <div class="left_color">Startup Color</div>
    <div class="right_color"><ColorSwatch v-bind:change="colorChange" v-bind:initial="initialColor"/></div>
    <div v-if="compLength() >= 4" class="left_color">Startup White</div>
    <div v-if="compLength() >= 4" class="right_universe">
      <input class="universefield" v-bind:style="{ border : validateComponentValue(3) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[3].value">
    </div>
    <div v-if="compLength() >= 5" class="left_color">Startup Warm White</div>
    <div v-if="compLength() >= 5" class="right_universe">
      <input class="universefield" v-bind:style="{ border : validateComponentValue(4) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[4].value">
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
    rgbTypes: Array,
    settings: Object,
    showConfigs: Array,
    settingsInternal: Object,
    rgbType: String
  },
  data() {
    return {
      componentTypes: [
          { text: 'Red', components : 3 },
          { text: 'Green', components : 3 },
          { text: 'Blue', components : 3 },
          { text: 'White', components : 4 },
          { text: 'White', components : 5 }
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
      compLength() {
        return this.rgbTypes[this.settings.rgbconfig[this.rgbIndex].outputtype].components;
      },
      dmxLength() {
        return this.rgbTypes[this.settings.rgbconfig[this.rgbIndex].outputtype].size == 16 ? 2 : 1;
      },
      dmxIndex(itemIndex) {
        return parseInt(this.settings.rgbconfig[this.rgbIndex].components[itemIndex].offset) + 1;
      },
      colorChange(color) {
        this.settings.rgbconfig[this.rgbIndex].components[0].value = color.r;
        this.settings.rgbconfig[this.rgbIndex].components[1].value = color.g;
        this.settings.rgbconfig[this.rgbIndex].components[2].value = color.b;
      },
      initialColor() {
        return { r: this.settings.rgbconfig[this.rgbIndex].components[0].value, 
                 g: this.settings.rgbconfig[this.rgbIndex].components[1].value, 
                 b: this.settings.rgbconfig[this.rgbIndex].components[2].value};
      },
      validateComponent(index) {
         index;
          return true;
      },
      checkInteger(value, min, max) {
        return (!isNaN(Number(value)) &&
                 parseInt(value) <= max &&
                 parseInt(value) >= min &&
                 Number(value) == parseInt(value));
      },
      validateArtnetUniverse(index) {
          var value = this.settings.rgbconfig[this.rgbIndex].components[index].artnet;
          if (this.checkInteger(value, 0, 32767)) {
            return true;
          } else {
            return false;
          }
      },
      validateE131Universe(index) {
          var value = this.settings.rgbconfig[this.rgbIndex].components[index].e131;
          if (this.checkInteger(value, 0, 32767)) {
            return true;
          } else {
            return false;
          }
      },
      validateComponentValue(index) {
          var value = this.settings.rgbconfig[this.rgbIndex].components[index].value;
          if (this.checkInteger(value, 0, 255)) {
            return true;
          } else {
            return false;
          }
      },
      validateChannelOffset(index) {
          var value = this.settings.rgbconfig[this.rgbIndex].components[index].offset;
          if (this.checkInteger(value, 0, 255)) {
            return true;
          } else {
            return false;
          }
      },
      componentsSplice() {
          var components = this.rgbTypes[this.settings.rgbconfig[this.rgbIndex].outputtype].components;
          function filterComponentType(componentType) {
            if (components >= componentType.components) {
              return true;
            }
            return false;
          }
          return this.componentTypes.filter(filterComponentType);
      },
      rgbTypeFiltered(showConfig, index) {
          function filterRGBType(rgbType) {
            if (showConfig.components[index] !== rgbType.components) {
              return false;
            }
            return true;
          }
          return this.rgbTypes.filter(filterRGBType);
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

*.offsetfield {
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
