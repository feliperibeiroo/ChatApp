<template>
    <li :style="`color: ${ color || 'black' };`">{{ msg }}</li>
</template>
<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
    props: {
        color: {
            type: String,
            required: false
        },
        msg: {
            type: String,
            required: true
        }
    }
})
</script>