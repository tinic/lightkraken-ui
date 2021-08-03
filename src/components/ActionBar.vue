<template>
  <div class="actions">
    <input
      class="button"
      @click="save"
      type="button"
      value="Save Configuration"
    >
    <input
      class="button"
      @click="reset"
      type="button"
      value="Reset Configuration"
    >
    <input
      class="button"
      @click="reboot"
      type="button"
      value="Reboot Into Upgrade Mode"
    >
    <div
      class="spacer"
      style="clear: both;"
    />
  </div>
</template> 

<script>
export default {
  name: "ActionBar",
  props: {
      baseURL: {
        type:String,
        default: "",
      },
      settings: {
        type:Object,
        default: function() {
          return { }
        }
      },
  },
  methods: {
      save() {
        this.axios
        .post(this.baseURL + 'settings', this.settings, { headers: { 'Content-Type': 'text/plain' }}) 
        .finally(function () {
          location.reload();
        });
      },
      reset() {
        this.axios
        .delete(this.baseURL + 'settings', {}, { headers: { 'Content-Type': 'text/plain' }}) 
        .finally(function () {
          location.reload();
        });
      },
      reboot() {
        this.axios.post(this.baseURL + 'bootloader', {}, { headers: { 'Content-Type': 'text/plain' }})
        .finally(function () {
          setTimeout(function(){
            location.reload();
          }, 10000);
        });
      }
  }
}
</script>

<style scoped>
*.actions {
  max-width: 760px;
  margin: 8px;
  align-content: center;
  text-align: center;
}

*.button {
  background-color: #0050AF; 
  border: none;
  color: white;
  margin: 4px;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  font-size: 16px;
  -webkit-transition-duration: 0.2s;
  transition-duration: 0.2s;
  border-radius: 8px;
}

.button:hover {
  background-color: rgb(92, 139, 226);
  color: white;
}

</style>
