<template>
  <div class="rgb">
    <div class="header">RGB {{ strip }}</div>
  </div>
</template>

<script>
export default {
  name: "RGBConfig",
  props: {
    strip: Number,
    settings: Object,
    settingsInternal: Object
  }
};
</script>

<style scoped>
*.rgb {
  width: 729px;
  height: 60px;
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
