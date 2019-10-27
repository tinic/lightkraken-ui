<template>
  <div class="output">
    <div class="header">Output Config</div>
    <div class="left">Output Mode</div>
    <div class="right">
      <select v-on:change="changeMode()" v-model="settings.outputmode">
        <option v-for="mode in outputModes" v-bind:value="mode.value" 
                v-bind:key="mode.id">{{ mode.text }}
        </option>
      </select>
    </div>
  </div>
</template> 

<script>
export default {
  name: "OutputConfig",
  props: {
    outputModes: Array,
    settings: Object,
    settingsInternal: Object
  },
  methods: {
      changeMode() {
         console.log(this.settings.outputmode);
      }
  }
};
</script>

<style scoped>
*.output {
  width: 729px;
  height: 60px;
  background-color: #eeeeee;
  padding: 10px;
  margin: 10px;
  border-radius: 16px;
}

*.mainsetting {
  height: 70px;
}

*.ip4setting {
  height: 120px;
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
