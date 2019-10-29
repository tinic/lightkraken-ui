<template>
  <div class="rgb" v-bind:style="{ height: panelHeight() + 'px' }">
    <div class="header">Analog RGB ({{ terminalNames[rgbIndex] }})</div>
    <div class="left">RGB type</div>
    <div class="right">
    <select v-model="settings.rgbconfig[rgbIndex].type">
      <option v-for="rgbType in rgbTypeFiltered(showConfigs[settings.outputmode], rgbIndex)" 
              v-bind:value="rgbType.value" v-bind:key="rgbType.id" >{{ rgbType.text }}
      </option>
    </select>
    </div>
		<div class="left_universe">DMX Mapping
    </div>
		<div class="right_universe">
      <div class="universelabel"><br></div>
      <div class="universefield">Universe</div>
      <div class="offsetfieldtitle">Offset</div>
      <div class="universe" v-for="(item, itemIndex) in componentsSplice()" v-bind:key="item.id">
        <div class="universelabel">{{ item.text }}</div>
        <input class="universefield" v-bind:style="{ border : validateComponent(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[itemIndex].universe">
        <input class="offsetfield" v-bind:style="{ border : validateComponent(itemIndex) ? '2px solid green' : '2px solid red' }" v-model="settings.rgbconfig[rgbIndex].components[itemIndex].offset">
        <span v-if="dmxLength() == 2" class="footnote">
           DMX Channels: <span style="font-weight:600;">{{ dmxIndex(itemIndex) }} ... {{ dmxIndex(itemIndex) + 1 }}</span>
        </span>
        <span v-if="dmxLength() == 1" class="footnote">
           DMX Channel: <span style="font-weight:600;">{{ dmxIndex(itemIndex) }}</span>
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
      panelHeight() {
        return 140 + this.componentsSplice().length * 32;
      },
      dmxLength() {
        return this.rgbTypes[this.settings.rgbconfig[this.rgbIndex].type].size == 16 ? 2 : 1;
      },
      dmxIndex(itemIndex) {
        return parseInt(this.settings.rgbconfig[this.rgbIndex].components[itemIndex].offset) + 1;
      },
      colorChange(color) {
        color;
      },
      validateComponent(index) {
         index;
          return true;
      },
      componentsSplice() {
          var components = this.rgbTypes[this.settings.rgbconfig[this.rgbIndex].type].components;
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
  font-weight: 600;
}

*.field {
  width: 80x;
}

*.universelabel {
  float: left;
  position: relative;
  top: 2px;
  left: 0px;
  width: 62px;
}

*.universefield {
  float: left;
  position: relative;
  left: 0px;
  width: 60px;
}

*.universetitle {
  float: left;
  position: relative;
  left: 0px;
  width: 60px;
  height: 25px;
}

*.offsetfield {
  float: left;
  position: relative;
  left: 15px;
  width: 40px;
}

*.offsetfieldtitle {
  float: left;
  position: relative;
  left: 20px;
  width: 60px;
  height: 25px;
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
  padding-top: 2px;
}

*.universe {
  float: left;
  height: 32px;
  width: 380px;
  padding-right: 10px;
}

*.footnote {
  float:left;
  padding-left: 40px;
  font-size: 80%;
  padding-top: 6px;
}

input.smallnumber {
    width:60px;
}

</style>
