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
    <div class="spacer" style="clear: both;"></div>
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
      }
  }
};
</script>

<style scoped>
*.output {
  max-width: 760px;
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
  height: 40px;
  font-size: large;
  font-weight: 600;
}

*.left {
  clear: left;
  float: left;
  width: 40%;
  margin: 2px;
  padding-top: 2px;
  text-align: right;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

*.right {
  float: left;
  overflow: auto;
  width: 50%;
  margin: 2px;
  padding-left: 10px;
  text-align: left;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

select {
    overflow:hidden; 
    white-space:nowrap; 
    text-overflow:ellipsis;
}
select option {
    text-overflow:ellipsis;
    overflow:hidden;
}

</style>
