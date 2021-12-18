<template>
  <label class="block">
    <span class="block text-sm font-medium text-gray-700"> {{ title }} </span>
    <slot />
  </label>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'Lable',
  props: ['title'],
})
</script>
