<template>
    <label class="block font-medium text-sm text-gray-400 mb-2">
        <span v-if="value">{{ value }}</span>
        <span v-else><slot /></span>
    </label>
</template>

<script>
    export default {
        props: ['value']
    }
</script>
