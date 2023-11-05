<template>
  <div>
    <slot></slot>
  </div>
</template>
<script setup>
const state = reactive({});
const events = reactive({});
const config = reactive({});
config.group = useRuntimeConfig().public.group;

const monthData = ref([]);

provide('state', state);
provide('events', events);
provide('config', config);

provide('monthData', monthData);
</script>
<style></style>
