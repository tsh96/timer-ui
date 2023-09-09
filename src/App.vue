<script lang="ts" setup>
import { darkTheme } from 'naive-ui'
</script>

<template lang="pug">
n-config-provider(:theme="darkTheme")
  router-view
</template>
