<template functional>
  <div class="tile card" v-bind="data.attrs" v-on="data.on">
    <ContextMenu class="tile-img">
      <router-link :to="props.to">
        <img v-if="props.image" :src="props.image">
        <img v-else src="@/shared/assets/fallback.svg">
      </router-link>
      <template #context-menu>
        <slot name="context-menu" />
      </template>
    </ContextMenu>
    <div class="card-body">
      <div class="text-truncate">
        <slot name="title">
          <router-link :to="props.to">
            <span class="font-weight-bold">{{ props.title }}</span>
          </router-link>
          <span v-if="props.subtitle" class="subtitle text-muted"> {{ props.subtitle }}</span>
        </slot>
      </div>
      <div class="text-truncate text-muted">
        <slot name="text">
          {{ props.text }}
        </slot>
      </div>
    </div>
  </div>
</template>
<style lang="scss">
  .tile-img {
    position: relative;
    width: 100%;

    img {
      position: absolute;
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }
  .subtitle {
    font-size: 14px;
  }
</style>
