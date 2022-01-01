<script setup lang="ts">
import PageNumbers from "../components/page-numbers.vue";
</script>

<template>
  <div class="slidev-layout default">
    <slot></slot>
    <PageNumbers></PageNumbers>
  </div>
</template>
