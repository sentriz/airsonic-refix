<template functional>
  <div :class="[data.class, data.staticClass]">
    <slot />
  </div>
</template>
<style scoped>
  ::v-deep a + a {
    margin-left: 12px;
  }
</style>
