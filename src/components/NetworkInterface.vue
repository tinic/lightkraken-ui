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
        <input class="ipsetting" v-bind:style="{ border : checkIPv4Address() ? '2px solid green' : '2px solid red' }" v-model="settings.ipv4address" type="text"/>
      </div>
      <div class="left">IPv4 Netmask</div>
      <div class="right">
        <input class="ipsetting" v-bind:style="{ border : checkIPv4Netmask() ? '2px solid green' : '2px solid red' }" v-model="settings.ipv4netmask" type="text"/>
      </div>
      <div class="left">IPv4 Gateway</div>
      <div class="right">
        <input class="ipsetting" v-bind:style="{ border : checkIPv4Gateway() ? '2px solid green' : '2px solid red' }" v-model="settings.ipv4gateway" type="text"/>
      </div>
    </div>
      <div class="left">
        <label for="tag">Description</label>
      </div>
      <div class="right">
        <textarea maxlength="255" class="text" id="tag" v-model="settings.tag"/>
      </div>
    <div class="spacer" style="clear: both;"></div>
  </div>
</template> 

<script>
export default {
  name: "NetworkInterface",
  props: {
    settings: Object
  },
  beforeMount() {
  },
  methods: {
    validateIPv4(address) {
            if (/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(address)) {
            return (true)
        }
        return false;
    },
    checkIPv4Address() {
      return this.validateIPv4(this.settings.ipv4address);
    },
    checkIPv4Netmask() {
      return this.validateIPv4(this.settings.ipv4netmask);
    },
    checkIPv4Gateway() {
      return this.validateIPv4(this.settings.ipv4gateway);
    }
  }
};
</script>

<style scoped>

*.status {
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
}

*.right {
  float: left;
  overflow: auto;
  width: 36%;
  min-width: 160px;
  max-width: 300px;
  margin: 2px;
  padding-left: 10px;
  text-align: left;
}

input.ipsetting {
    width:140px;
}

textarea.text {
  height:50px;
  width: 90%;
  color: black;
  font-size: 15px;
  font-weight: 300;
  text-align: left-top;
}

</style>
