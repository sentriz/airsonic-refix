<template functional>
  <a :href="props.href"
     target="_blank"
     rel="noopener noreferrer"
     class="d-inline-flex align-items-center"
     style="gap: 5px;"
     :class="[data.class, data.staticClass]"
  >
    <slot />
    <Icon icon="link" />
  </a>
</template>
