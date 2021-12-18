<template>
    <button v-bind="value" class="py-3 px-6 text-white rounded-lg bg-purple-600 shadow-lg block md:inline-block disabled:bg-purple-300">
        
        <slot />
    </button>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
    name: "Button",
    props: ['value'],
})
</script>