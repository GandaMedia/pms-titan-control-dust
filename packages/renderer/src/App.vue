<template>
  <titan-connection-check />
  <div class="mx-auto p-4 sm:p-6 lg:p-8">
    <div class="flex">
      <div class="flex-grow">
        <button-grid
          @fire-playback="firePlayback"
          @kill-playback="killPlayback"
        />
      </div>
      <master-fader
        v-if="masterFader !== null"
        :titan_ip="setupData.titan_ip"
        :titan-id="mfTitanId"
        :title="mfTitle"
      />
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

export default {
  components: {
    MasterFader,
    TitanConnectionCheck,
    ButtonGrid,
  },
  data: function () {
    return {
      masterFader: null,
      setupData: setupJson,
    };
  },
  computed: {
    mfTitle: function () {
      if (this.masterFader !== null){
        return this.masterFader?.title ?? 'ERROR';
      }
      return 'ERROR';
    },
    mfTitanId: function () {
      if (this.masterFader !== null){
        return this.masterFader?.titanId ?? '0';
      }
      return '0';
    },
  },
  mounted() {
    this.masterFader = settingsJson.master_fader ?? null;
  },
  methods: {
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
input[type=range][orient=vertical]
{
  writing-mode: bt-lr; /* IE */
  -webkit-appearance: slider-vertical; /* WebKit */
  width: 8px;
  height: 100%;
  padding: 0 5px;
}
</style>

