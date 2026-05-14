<template>
  <td class="d-none d-lg-table-cell">
    <ArtistLinks :artists="track.artists" :display-artist="track.displayArtist" />
  </td>
</template>
<script lang="ts">
  import { defineComponent, PropType } from 'vue'
  import ArtistLinks from '@/library/artist/ArtistLinks.vue'
  import { Track } from '@/shared/api'

  export default defineComponent({
    components: { ArtistLinks },
    props: {
      track: { type: Object as PropType<Track>, required: true }
    }
  })
</script>
