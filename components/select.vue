<template>
  <select
    v-bind:value="value"
    @input="$emit('input', $event.target.value)"
    class="block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-primary-500 focus:border-primary-500 invalid:border-pink-500 invalid:text-pink-600 focus:invalid:border-pink-500 focus:invalid:ring-pink-500"
  >
    <option
      v-for="(item, index) in items"
      :key="index"
      :value="item.value"
      :selected="items.selected ? true : false"
    >
      {{ item.text }}
    </option>
  </select>
</template>

<script lang="ts">
import Vue from 'vue'

import SelectOption from '@/interfaces/selectOption'

export default Vue.extend({
  props: {
    items: {
      type: Array as () => SelectOption[],
      required: true,
    },
    value: {
      type: String,
      required: false,
    },
  },
})
</script>
