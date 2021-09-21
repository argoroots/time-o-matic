<template>
  <div class="flex h-screen">
    <div class="m-auto">
      <h1 class="mb-12 font-bold text-5xl text-center text-blue-600">
        Time-O-Matic
      </h1>
      <input
        v-model="time"
        class="text-4xl text-center outline-none"
        autofocus
        placeholder=" 1d 2h 45m"
      >
      <div class="my-2 text-xl text-center text-gray-500">
        X
      </div>
      <input
        v-model="multiplier"
        class="my-2 text-4xl text-center outline-none"
        placeholder="1"
      >
      <div class="my-2 text-xl text-center text-gray-500">
        =
      </div>
      <input
        v-model="newTime"
        class="text-4xl text-center outline-none bg-white"
        readonly
        disabled
      >
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const multiplier = ref('1.3')
const time = ref('3h 24m')

const getValue = (values, key) => {
  const keyValues = values.filter(x => x.includes(key))
  if (keyValues.length === 0) { return 0 }
  return keyValues.map(x => parseFloat(x) || 0).reduce((p, c) => p + c)
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
  const minutes = getMinutes(time.value)
  const multiplierFloat = parseFloat(multiplier.value.replace(',', '.'))
  return `${Math.round(multiplierFloat * minutes / 0.6) / 100}h`
})

</script>
