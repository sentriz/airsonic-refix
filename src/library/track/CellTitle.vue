<template>
  <td>
    <slot>
      <template v-for="(part, i) in titleParts">
        <router-link v-if="part.value?.artist?.id" :key="i"
                     :to="{name: 'artist', params: { id: part.value.artist.id }}"
                     class="contributor-link"
                     @click.native.stop>{{ part.text }}</router-link>
        <template v-else>{{ part.text }}</template>
      </template>
    </slot>
    <div v-if="track.description" class="text-muted">
      <small>{{ track.description }}</small>
    </div>
    <div v-else-if="track.artists.length" class="d-lg-none text-muted">
      <small>{{ formatArtists(track.artists) }}</small>
    </div>
  </td>
</template>
<script lang="ts">
  import { defineComponent, PropType } from 'vue'
  import { formatArtists, splitByLabels } from '@/shared/utils'
  import { Track } from '@/shared/api'

  export default defineComponent({
    props: {
      track: { type: Object as PropType<Track>, required: true }
    },
    computed: {
      titleParts() {
        return splitByLabels(this.track.title, this.track.contributors || [], c => c.artist?.name || '')
      }
    },
    methods: {
      formatArtists
    }
  })
</script>
<style scoped>
  .contributor-link {
    text-decoration: underline;
    text-decoration-color: var(--bs-secondary-color);
  }
</style>
