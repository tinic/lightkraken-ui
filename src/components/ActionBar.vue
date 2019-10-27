<template>
  <div class="actions">
        <input class="button" v-on:click="save" type="button" value="Save Configuration" />
        <input class="button" type="button" value="Reset Configuration" />
        <input class="button" type="button" value="Reboot Into Upgrade Mode" />
  </div>
</template> 

<script>
export default {
  name: "ActionBar",
  props: {
      settings:Object
  },
  methods: {
      save() {
      },
      reset() {
      },
      reboot() {
      }
  }
}
</script>

<style scoped>
*.actions {
  width: 729px;
  align-content: center;
}

*.button {
  background-color: #0050AF; /* Green */
  border: none;
  color: white;
  margin: 4px;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  -webkit-transition-duration: 0.2s;
  transition-duration: 0.2s;
}

.button:hover {
  background-color: rgb(92, 139, 226); /* Green */
  color: white;
}

</style>
