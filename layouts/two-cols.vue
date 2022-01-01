<script setup lang="ts">
import PageNumbers from "../components/page-numbers.vue";
const props = defineProps({
  class: {
    type: String,
  },
});
</script>

<template>
  <div class="slidev-layout two-columns w-full h-full grid grid-cols-2">
    <div class="col-left" :class="props.class">
      <slot />
    </div>
    <div class="col-right" :class="props.class">
      <slot name="right" />
    </div>
    <PageNumbers></PageNumbers>
  </div>
</template>
