<template>
  <div class="strip">
    <div class="header">Strip {{ strip }}</div>
    <div class="left">LED type</div>
    <div class="right">
    <select v-model="settings.stripconfig[strip].type">
      <option v-for="stripType in stripTypesFiltered(showConfigs[settings.outputmode])" 
              v-bind:value="stripType.value" v-bind:key="stripType.id" >{{ stripType.text }}
      </option>
    </select>
    </div>
    <div class="left">Startup Color</div>
    <div class="right"><ColorSwatch v-bind:change="colorChange"/></div>
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
      colorChange(color) {
        color;
      },
      stripTypesFiltered(showConfig) {
          function filterStripType(stripType) {
            if (showConfig.clock == 0 &&
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
    strip: Number,
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
  height: 120px;
  background-color: #eeeeee;
  padding: 10px;
  margin: 10px;
  border-radius: 16px;
}

*.header {
  height: 33px;
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
</style>
