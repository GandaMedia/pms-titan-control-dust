<template>
  <div class="ml-10">
    <div class="bg-white overflow-hidden shadow rounded-lg">
      <div class="px-4 pt-5 sm:px-6">
        <span class="px-3 bg-white text-lg font-medium text-gray-900 break-words">
          {{ title }} ({{ bpm }}bpm)
        </span>
      </div>
      <div class="px-4 py-3 sm:p-6">
        <input
          v-model="level"
          type="range"
          orient="vertical"
          class="mx-auto shadow-sm block w-full border-gray-300 rounded-md"
        />
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import axios from 'axios';

export default {
  props: {
    titanIp: {
      required: true,
      type: String,
    },
    titanId: {
      required: true,
      type: String,
    },
    title: {
      required: true,
      type: String,
    },
  },
  data: function () {
    return {
      level: 0,
    };
  },
  computed: {
    bpm: function () {
      return this.level * 3;
    },
  },
  watch: {
    level: function (level) {
      let newLevel = level / 100;

      let url =
        'http://' +
        this.titan_ip +
        ':4430/titan/script/2/Masters/SetSpeed?handle_titanId=' +
        this.titanId +
        '&value=' +
        newLevel * 300;

      axios.get(url);
    },
  },
  mounted() {
    this.$nextTick(function () {
      window.setInterval(() => {
        // this.sendLevel();
      }, 1000);
    });
  },
  methods: {
    getFaderLevel: function () {
      // todo
    },
    sendLevel: function () {
      let newLevel = this.level / 100;

      let url =
        'http://' +
        this.titan_ip +
        ':4430/titan/script/2/Masters/SetSpeed?handle_titanId=' +
        this.titanId +
        '&value=' +
        newLevel * 300;

      axios.get(url);
    },
  },
};
</script>
