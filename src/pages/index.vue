<script setup lang="ts" generic="T extends any, O extends any">
const { vibrate, isSupported } = useVibrate()

const seconds = ref(10)
const count = ref(0)
const lastTick = ref(Date.now())
const { timestamp, isActive, resume: resumeTimestamp, pause: pauseTimestamp } = useTimestamp({ controls: true })

function pad(num: number, size: number) {
  const s = `${num}`
  return s.padStart(size, '0')
}

const timeElapsed = computed(() => {
  const milliseconds = timestamp.value - lastTick.value
  const seconds = Math.floor(milliseconds / 1000)
  const minutes = Math.floor(seconds / 60)
  return `${pad(minutes, 2)}:${pad(seconds % 60, 2)}:${pad(Math.floor((milliseconds / 10) % 100), 2)}`
})

whenever(() => {
  return timestamp.value - lastTick.value > seconds.value * 1000
}, () => {
  count.value++
  lastTick.value = timestamp.value
  vibrate(500)
})

function pause() {
  pauseTimestamp()
}

function resume() {
  lastTick.value = Date.now() - (timestamp.value - lastTick.value)
  resumeTimestamp()
}

pause()

function reset() {
  count.value = 0
  lastTick.value = timestamp.value
}
</script>

<template lang="pug">
.absolute.inset-0.flex.items-center.justify-center
  .flex.flex-col.items-center.justify-center
    .text Seconds:
    n-input-number(v-model:value="seconds" min="1" step="1" size="large" style="width: 50vw")
    n-divider
    .text Count:
    .text-6xl {{ count }}
    .flex.gap-x-4.py-4
      n-button(v-if="isActive" @click="pause") #[.i-mdi-pause]
      n-button(v-if="!isActive" @click="resume") #[.i-mdi-play]
      n-button(v-if="!isActive" @click="reset") #[.i-mdi-refresh]
    .text-2xl.font-mono {{ timeElapsed }}

.fixed.bottom-4.left-4.flex.items-center.gap-x-2.text-lg(v-if="isSupported" @click="vibrate(500)")
  .i-mdi-vibrate
  | supported
</template>
