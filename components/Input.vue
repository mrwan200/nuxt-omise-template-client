<template>
  <input
    :type="type"
    v-bind:value="value"
    @input="$emit('input', $event.target.value)"
    class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md text-sm shadow-sm placeholder-gray-400 focus:outline-none focus:border-sky-500 focus:ring-1 focus:ring-sky-500 disabled:bg-gray-50 disabled:text-gray-500 disabled:border-gray-200 disabled:shadow-none"
  />
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
    name: "Input",
    props: ['value','type'],

})
</script>