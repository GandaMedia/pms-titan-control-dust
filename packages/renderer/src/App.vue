<template>
  <titan-connection-check />
  <div class="mx-auto p-4 sm:p-6 lg:p-8">
    <div class="flex">
      <div class="flex-grow">
        <button-grid
          @fire-playback="firePlayback"
          @kill-playback="killPlayback"
          @kill-all-playbacks="killAllPlaybacks"
          @trigger-playback="triggerPlayback"
          @trigger-autoplay="triggerAutoplay"
        />
      </div>
      <div class="grid grid-cols-1 gap-4">
        <div
          v-for="fader in faders"
          :key="fader.name"
        >
          <master-fader
            :titan-ip="setupData.titan_ip"
            :titan-id="fader.titanId.toString()"
            :title="fader.title"
          />
        </div>
        <rate-fader
          :titan-ip="setupData.titan_ip"
          titan-id="1612"
          title="Speed"
        />
      </div>
    </div>
  </div>
</template>

<script>

import ButtonGrid from './components/ButtonGrid.vue';
import TitanConnectionCheck from './components/TitanConnectionCheck.vue';
import MasterFader from './components/MasterFader.vue';
import settingsJson from '../settings.json';
import setupJson from '../setup.json';
import axios from 'axios';
import RateFader from '/@/components/RateFader.vue';

export default {
  components: {
    MasterFader,
    RateFader,
    TitanConnectionCheck,
    ButtonGrid,
  },
  data: function () {
    return {
      masterFader: null,
      rateFader: null,
      setupData: setupJson,
      faders: settingsJson.faders,
      show_triggers: settingsJson.show_triggers,
    };
  },
  computed: {
    mfTitle: function () {
      if (this.masterFader !== null) {
        return this.masterFader?.title ?? 'ERROR';
      }
      return 'ERROR';
    },
    mfTitanId: function () {
      if (this.masterFader !== null) {
        return this.masterFader?.titanId ?? '0';
      }
      return '0';
    },
  },
  mounted() {
    // this.masterFader = settingsJson.master_fader ?? null;
    // this.faders = settingsJson.faders;
  },
  methods: {
    triggerPlayback: function (tid) {
      // let ip = this.setupData.titan_ip;
      // this.show_triggers.forEach(function (trigger) {
      //   console.log(trigger);
      //   let url = 'http://'
      //     + ip
      //     + ':4430/titan/script/2/Playbacks/KillAllPlaybacks';
      //   axios
      //     .get(url);
      // });

      let triggerUrl = 'http://'
        + this.setupData.titan_ip
        + ':4430/titan/script/2/Playbacks/FirePlaybackAtLevel?handle_titanId=' + tid
        + '&level_level=1'
        + '&alwaysRefire=false';
      // axios
      //   .get(url)
      //   .then(function () {
      //     axios
      //       .get(triggerUrl);
      //   });

      axios.get(triggerUrl);
    },
    triggerAutoplay: function () {
      let url = 'http://'
        + this.setupData.titan_ip
        + ':4430/titan/script/2/Playbacks/KillAllPlaybacks';
      let triggerUrl = 'http://'
        + this.setupData.titan_ip
        + ':4430/titan/script/2/CueLists/Play?handle_titanId=8827';
      axios
        .get(url)
        .then(function () {
          axios
            .get(triggerUrl);
        });
    },
    firePlayback: function (tid) {
      let url = 'http://'
        + this.setupData.titan_ip
        + ':4430/titan/script/2/Playbacks/FirePlaybackAtLevel?handle_titanId=' + tid
        + '&level_level=1'
        + '&alwaysRefire=false';
      axios
        .get(url);
    },
    killPlayback: function (tid) {
      let url = 'http://'
        + this.setupData.titan_ip
        + ':4430/titan/script/2/Playbacks/KillPlayback?handle_titanId=' + tid;
      axios
        .get(url);
    },
    killAllPlaybacks: function () {
      let url = 'http://'
        + this.setupData.titan_ip
        + ':4430/titan/script/2/Playbacks/KillAllPlaybacks';
      axios
        .get(url);
    },
  },
};


</script>

<style>
input[type=range][orient=vertical] {
  writing-mode: bt-lr; /* IE */
  -webkit-appearance: slider-vertical; /* WebKit */
  width: 8px;
  height: 100%;
  padding: 0 5px;
}
</style>

