<template>
  <div class="status">
    <div class="header">Current Status</div>
    <div class="item">
    <span class="left">Firmware revision</span>
    <span class="statusfield">{{ status.buildnumber }}</span>
    </div>
    <div class="item">
    <span class="left">IPv4 address</span>
    <span class="statusfield">{{ status.ipv4address }}</span>
    </div>
    <div class="item">
    <span class="left">IPv4 netmask</span>
    <span class="statusfield">{{ status.ipv4netmask }}</span>
    </div>
    <div class="item">
    <span class="left">IPv4 gateway</span>
    <span class="statusfield">{{ status.ipv4gateway }}</span>
    </div>
    <div class="item">
    <span class="left">MAC Address</span>
    <span class="statusfield">{{ status.macaddress }}</span>
    </div>
    <div class="item">
    <span class="left">Hostname</span>
    <span class="statusfield">{{ status.hostname }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "Status",
  props: {
    status: Object
  }
};
</script>

<style scoped>
*.status {
  width: 729px;
  height: 244px;
  background-color: #ddeedd;
  padding: 10px;
  margin: 10px;
  border-radius: 16px;
  margin-bottom: 0px;
}

*.item {
  height: 33px;
}

*.header {
  height: 40px;
  font-size: large;
  font-weight: bold;
}

*.left {
  clear: left;
  float: left;
  width: 40%;
  margin: 0 0 0.6em;
  padding: 3px;
  text-align: right;
}

*.right {
  overflow: auto;
  margin: 0 0 0.6em 0.4em;
  padding-left: 0.6em;
  text-align: left;
}

*.statusfield {
  font-family: inherit;
  font-size: 100%;
  font-weight: 600;
  width: 30%;
  float: left;
  background-color: #eeffee;
  overflow: auto;
  margin: 0 0 0.6em 0.4em;
  text-align: left;
  padding: 3px;
  padding-left: 10px;
  border-radius: 8px;
}
</style>
