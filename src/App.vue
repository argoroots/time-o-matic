<template>
  <div class="h-screen flex flex-wrap content-center">
    <h1 class="w-full mb-12 font-bold text-5xl text-center text-blue-600">
      Time-O-Matic
    </h1>
    <input
      v-model="time"
      class="w-full text-4xl text-center outline-none"
      autofocus
      placeholder="1d 2h 45m"
    >
    <div class="w-full my-2 text-xl text-center text-gray-500">
      X
    </div>
    <input
      v-model="multiplier"
      class="w-full my-2 text-4xl text-center outline-none"
      placeholder="1"
    >
    <div class="w-full my-2 text-xl text-center text-gray-500">
      =
    </div>
    <input
      v-model="newTime"
      class="w-full text-4xl text-center outline-none bg-white"
      readonly
      disabled
      placeholder="..."
    >
    <div
      class="w-full text-center my-2 cursor-pointer text-gray-500 hover:text-blue-600"
      title="Copy to Clipboard"
      @click="copyResult"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        class="h-5 w-5 inline"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3"
        />
      </svg>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const multiplier = ref('1.3')
const time = ref('3h 24m')

const getValue = (values, key) => {
  const keyValues = values.filter(x => x.includes(key))

  if (keyValues.length === 0) {
    return 0
  } else {
    return keyValues.map(x => parseFloat(x) || 0).reduce((p, c) => p + c)
  }
}

const getMinutes = (str) => {
  const parts = str
    .toLowerCase()
    .replace(/\s+/g, ' ')
    .replace(/,/g, '.')
    .replace(/\sd|d/g, 'd ')
    .replace(/\sh|h/g, 'h ')
    .replace(/\sm|m/g, 'm ')
    .split(' ')

  const days = getValue(parts, 'd')
  const hours = getValue(parts, 'h')
  const minutes = getValue(parts, 'm')

  return minutes + hours * 60 + days * 60 * 8
}

const newTime = computed(() => {
  window.plausible('Calculate')

  const minutes = getMinutes(time.value)
  const multiplierFloat = parseFloat(multiplier.value.replace(',', '.')) || 1
  const hours = Math.round(multiplierFloat * minutes / 0.6) / 100

  return hours ? `${hours}h` : ''
})

function copyResult () {
  window.plausible('Copy')

  navigator.clipboard.writeText(newTime.value)
}
</script>
