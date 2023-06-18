<template>
  <div class="card">
    <div class="px-6 py-4 max-md:px-5 max-md:py-3 max-sm:px-4 max-sm:py-2 max-xs:px-3 max-xs:py-1.5">
      <div class="font-bold text-xl max-md:text-lg max-sm:text-base mb-2 max-md:mb-1">
        {{ comment.username }}
      </div>
      <p class="text-gray-700 text-base max-md:text-sm max-sm:text-xs">
        {{ comment.comment }}
      </p>
    </div>
  </div>
</template>

<script setup>
  const { comment } = defineProps(['comment'])
</script>

<style scoped>

</style>