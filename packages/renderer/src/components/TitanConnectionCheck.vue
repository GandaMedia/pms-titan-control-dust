<!-- This example requires Tailwind CSS v2.0+ -->
<template>
  <div
    v-show="connectionError"
    class="relative bg-red-600"
  >
    <div class="max-w-7xl mx-auto py-3 px-3 sm:px-6 lg:px-8">
      <div class="pr-16 sm:text-center sm:px-16">
        <p class="font-medium text-white">
          <span class="md:hidden">
            TITAN SERVER NOT CONNECTED
          </span>
          <span class="hidden md:inline">
            TITAN SERVER NOT CONNECTED AT {{ setupData.titan_ip }}
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import setupJson from '../../setup.json';
import axios from 'axios';

export default {
  components: {
  },
  data: function () {
    return {
      setupData: setupJson,
      interval: null,
      connectionError: true,
    };
  },
  created() {
    this.checkTitanPresence();
    this.interval = setInterval(() => this.checkTitanPresence(), 30000);
  },
  beforeUnmount() {
    clearInterval();
  },
  methods: {
    titanIsConnected: function(){
      return axios
        .get('http://' + this.setupData.titan_ip + ':4430/titan/get/System/SoftwareVersion')
        .then(function () {
          // handle success
          return false;

        })
        .catch(function () {
          // handle error
          return true;
        });    },
    checkTitanPresence: function () {
      this.connectionError = this.titanIsConnected().then(result => this.connectionError = result);
    },
  },
};

//
// function handleErrors(response) {
//   if (!response.ok) {
//     alert('ERROR');
//     throw Error(response.statusText);
//   }
//   return response;
// }
//
// function checkTitanPresence() {
//   const fs = require('fs');
//   let setupData = JSON.parse(fs.readFileSync('setup.json'));
//
//   let url = 'http://' + setupData.titan_ip + ':4430/titan/get/System/SoftwareVersion'
//   fetch(url)
//       .then(handleErrors)
//       .then(function (response) {
//         console.log("ok");
//       }).catch(function (error) {
//         alert('Titan Server not connecting...');
//         app.exit(0)
//       }
//   );
// }
//
//
// checkTitanPresence();

</script>

<style scoped>

</style>
