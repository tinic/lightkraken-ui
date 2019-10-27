<template>
  <div class="status">
    <div class="header">Network Interface</div>
    <div class="mainsetting">
      <div class="left">
        <label for="broadcast">Accept DMX broadcast</label>
      </div>
      <div class="right">
        <input class="checkbox" type="checkbox" id="broadcast" v-model="settings.broadcast">
      </div>
      <div class="left">
        <label for="dhcp">Use DHCP</label>
      </div>
      <div class="right">
        <input class="checkbox" type="checkbox" id="dhcp" v-model="settings.dhcp">
      </div>
    </div>
    <div class="ip4setting" v-if="settings.dhcp !== undefined && settings.dhcp == false">  
      <div class="left">IPv4 Address</div>
      <div class="right">
        <input class="valid" v-bind:class="{invalid : settingsInternal.ipv4addressInvalid}" 
                             v-on:keyup="checkIPv4()" v-model="settings.ipv4address" type="text"/>
      </div>
      <div class="left">IPv4 Netmask</div>
      <div class="right">
        <input class="valid" v-bind:class="{invalid : settingsInternal.ipv4netmaskInvalid}" 
                             v-on:keyup="checkIPv4()" v-model="settings.ipv4netmask" type="text"/>
      </div>
      <div class="left">IPv4 Gateway</div>
      <div class="right">
        <input class="valid" v-bind:class="{invalid : settingsInternal.ipv4gatewayInvalid}" 
                             v-on:keyup="checkIPv4()" v-model="settings.ipv4gateway" type="text"/>
      </div>
    </div>
  </div>
</template> 

<script>
export default {
  name: "NetworkInterface",
  props: {
    settings: Object,
    settingsInternal: Object,
  },
  beforeMount() {
    this.checkIPv4()
  },
  methods: {
    validateIPv4(address) {
            if (/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(address)) {
            return (true)
        }
        return false;
    },
    checkIPv4() {
      this.settingsInternal.ipv4addressInvalid = this.validateIPv4(this.settings.ipv4address) === false;
      this.settingsInternal.ipv4netmaskInvalid = this.validateIPv4(this.settings.ipv4netmask) === false;
      this.settingsInternal.ipv4gatewayInvalid = this.validateIPv4(this.settings.ipv4gateway) === false;
    }
  }
};
</script>

<style scoped>

*.status {
  width: 729px;
  border-top: 1px solid black;
  background-color: #eeeeee;
  border-bottom: 1px solid black;
  padding: 10px;
  margin: 10px;
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

.valid {
  border: 2px solid green;
}

.invalid {
  border: 2px solid red;
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
  width: 30%;
  margin: 0 0 0.6em 0.4em;
  padding-left: 0.6em;
  text-align: left;
}
</style>
