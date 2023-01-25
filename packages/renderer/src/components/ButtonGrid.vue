<template>
  <div class="relative mb-2">
    <div
      class="absolute inset-0 flex items-center"
      aria-hidden="true"
    >
      <div class="w-full border-t border-gray-300" />
    </div>
    <div class="relative flex justify-start">
      <span class="pr-2 bg-white text-sm text-gray-500"> Effects </span>
    </div>
  </div>
  <ul
    role="list"
    class="grid grid-cols-2 gap-x-4 gap-y-8 sm:grid-cols-3 sm:gap-x-6 md:grid-cols-8 xl:gap-x-6"
  >
    <li
      v-for="button in effects"
      :key="button.source"
      class="relative"
    >
      <div class="group block w-full aspect-w-10 aspect-h-7 rounded-lg bg-gray-100 overflow-hidden">
        <img
          :src="button.source"
          alt=""
          class="object-cover pointer-events-none"
        />
        <button
          type="button"
          class="absolute inset-0 focus:outline-none"
          :class="[
            button.active ? 'bg-green-500 opacity-50' : '',
            button.down && button.type !== 'toggle' ? 'bg-black opacity-50' : '',
          ]"
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
      <span class="pr-2 bg-white text-sm text-gray-500"> Playbacks </span>
    </div>
    <ul
      role="list"
      class="grid grid-cols-2 gap-x-4 gap-y-8 sm:grid-cols-3 sm:gap-x-6 md:grid-cols-8 xl:gap-x-6"
    >
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
            class="object-cover pointer-events-none"
          />
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

<script setup lang="ts">
import settingsJson from '../../settings.json';
import {onBeforeMount, onMounted, ref} from 'vue';

const emit = defineEmits([
  'firePlayback',
  'killPlayback',
  'killAllPlaybacks',
  'triggerPlayback',
  'triggerAutoplay',
]);

const interval = ref();
const effects = ref(settingsJson.effects);
const show_triggers = ref(settingsJson.show_triggers);

function triggerShow(button) {
  emit('triggerPlayback', button.titanId);
}
// function triggerAutoplay() {
//   emit('triggerAutoplay');
// }
function pushEffect(button) {
  button.down = true;
  if (button.type === 'toggle') {
    if (button.active) {
      emit('killPlayback', button.titanId);
    } else {
      emit('firePlayback', button.titanId);
    }
    button.active = !button.active;
  } else {
    emit('firePlayback', button.titanId);
  }
}
function releaseEffect(button) {
  button.down = false;
  if (button.type === 'instant') {
    emit('killPlayback', button.titanId);
  }
}
function checkToggleState() {
  let toggleButtons = effects.value.filter(function (button) {
    return button.type === 'toggle';
  });
  toggleButtons.forEach(function (button) {
    console.log(button);
  });
}

onBeforeMount(() => {
  checkToggleState();
  interval.value = setInterval(() => {
    checkToggleState();
  }, 30000);
});

onMounted(() => {
  for (const effect of effects.value) {
    effect.active = false;
    effect.down = false;
  }
});
</script>
