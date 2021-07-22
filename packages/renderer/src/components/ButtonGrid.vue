<template>
  <div class="relative mb-2">
    <div
      class="absolute inset-0 flex items-center"
      aria-hidden="true"
    >
      <div class="w-full border-t border-gray-300" />
    </div>
    <div class="relative flex justify-start">
      <span class="pr-2 bg-white text-sm text-gray-500">
        Effects
      </span>
    </div>
  </div>
  <ul
    role="list"
    class="grid grid-cols-2 gap-x-4 gap-y-8 sm:grid-cols-3 sm:gap-x-6 md:grid-cols-5  xl:gap-x-8"
  >
    <li
      v-for="button in effects"
      :key="button.source"
      class="relative"
    >
      <div
        class="group block w-full aspect-w-10 aspect-h-7 rounded-lg bg-gray-100 overflow-hidden"
      >
        <img
          :src="button.source"
          alt=""
          class="object-cover pointer-events-none "
        >
        <button
          type="button"
          class="absolute inset-0 focus:outline-none"
          :class="[button.active ? 'bg-green-500 opacity-50' : '', button.down && button.type !== 'toggle' ? 'bg-black opacity-50' : '']"
          @mousedown="pushEffect(button)"
          @mouseup="releaseEffect(button)"
        >
          <span class="sr-only">View details for {{ button.title }}</span>
        </button>
      </div>
      <p class="mt-2 block text-sm font-medium text-gray-900 truncate pointer-events-none">
        {{ button.title }}
      </p>
    </li>
  </ul>
  <div class="relative mt-4 mb-2">
    <div
      class="absolute inset-0 flex items-center"
      aria-hidden="true"
    >
      <div class="w-full border-t border-gray-300" />
    </div>
    <div class="relative flex justify-start">
      <span class="pr-2 bg-white text-sm text-gray-500">
        Playbacks
      </span>
    </div>
    <ul
      role="list"
      class="grid grid-cols-2 gap-x-4 gap-y-8 sm:grid-cols-3 sm:gap-x-6 md:grid-cols-5  xl:gap-x-8"
    >
      <!--      <li class="relative">-->
      <!--        <div-->
      <!--          class="group block w-full aspect-w-10 aspect-h-7 rounded-lg bg-gray-100 overflow-hidden"-->
      <!--        >-->
      <!--          <img-->
      <!--            src="https://images.unsplash.com/photo-1496843916299-590492c751f4?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=512&q=80"-->
      <!--            alt=""-->
      <!--            class="object-cover pointer-events-none "-->
      <!--          >-->
      <!--          <button-->
      <!--            type="button"-->
      <!--            class="absolute inset-0 focus:outline-none hover:bg-black hover:opacity-50 focus:bg-green-500 focus:opacity-50"-->
      <!--            @click="triggerAutoplay"-->
      <!--          >-->
      <!--            <span class="sr-only">AUTOPLAY</span>-->
      <!--          </button>-->
      <!--        </div>-->
      <!--        <p class="mt-2 block text-sm font-medium text-gray-900 truncate pointer-events-none">-->
      <!--          AUTOPLAY-->
      <!--        </p>-->
      <!--      </li>-->

      <li
        v-for="button in show_triggers"
        :key="button.source"
        class="relative"
      >
        <div
          class="group block w-full aspect-w-10 aspect-h-7 rounded-lg bg-gray-100 overflow-hidden"
        >
          <img
            :src="button.source"
            alt=""
            class="object-cover pointer-events-none "
          >
          <button
            type="button"
            class="absolute inset-0 focus:outline-none hover:bg-black hover:opacity-50 focus:bg-green-500 focus:opacity-50"
            @click="triggerShow(button)"
          >
            <span class="sr-only">View details for {{ button.title }}</span>
          </button>
        </div>
        <p class="mt-2 block text-sm font-medium text-gray-900 truncate pointer-events-none">
          {{ button.title }}
        </p>
      </li>
    </ul>
  </div>
</template>

<script>
import settingsJson from '../../settings.json';

export default {
  emits: ['firePlayback', 'killPlayback', 'killAllPlaybacks', 'triggerPlayback', 'triggerAutoplay'],
  data: function () {
    return {
      effects: settingsJson.effects,
      show_triggers: settingsJson.show_triggers,
    };
  },
  mounted() {
    this.effects.forEach(function (effect) {
      effect.active = false;
      effect.down = false;
    });
  },
  created() {
    this.checkToggleState();
    this.interval = setInterval(() => this.checkToggleState(), 30000);
  },
  methods: {
    triggerShow: function (button) {
      this.$emit('triggerPlayback', button.titanId);
    },
    triggerAutoplay: function () {
      this.$emit('triggerAutoplay');
    },
    pushEffect: function (button) {
      button.down = true;
      if (button.type === 'toggle') {
        if (button.active) {
          this.$emit('killPlayback', button.titanId);
        } else {
          this.$emit('firePlayback', button.titanId);
        }
        button.active = !button.active;
      } else {
        this.$emit('firePlayback', button.titanId);
      }
    },
    releaseEffect: function (button) {
      button.down = false;
      if (button.type === 'instant') {
        this.$emit('killPlayback', button.titanId);

      }
    },
    checkToggleState: function () {
      let toggleButtons = this.effects.filter(function (button) {
        return button.type === 'toggle';
      });
      toggleButtons.forEach(function (button) {
        console.log(button);
      });
    },
  },
};
</script>
