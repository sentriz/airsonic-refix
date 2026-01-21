<template>
  <BaseTable>
    <BaseTableHead>
      <th v-if="!noArtist" class="text-start d-none d-lg-table-cell">
        Artist
      </th>
      <th v-if="!noAlbum" class="text-start d-none d-md-table-cell">
        Album
      </th>
      <th v-if="!noDuration" class="text-end d-none d-md-table-cell">
        Duration
      </th>
    </BaseTableHead>
    <tbody>
      <template v-for="[discNum, discTracks] in Object.entries(groupedTracks)">
        <tr v-if="hasMultipleDiscs" :key="discNum" class="disc-header">
          <td colspan="10">{{ album.discTitles?.[discNum] || `Disc ${Number(discNum) + 1}` }}</td>
        </tr>
        <tr v-for="item in discTracks" :key="`${discNum}-${item.id}`"
            :class="{'active': item.id === playingTrackId}"
            :draggable="true" @dragstart="dragstart(item, $event)"
            @click="play(tracks.indexOf(item))">
          <CellTrackNumber
            :active="item.id === playingTrackId && isPlaying"
            :value="item.track"
          />
          <CellTitle :track="item" />
          <CellArtist v-if="!noArtist" :track="item" />
          <CellAlbum v-if="!noAlbum" :track="item" />
          <CellDuration v-if="!noDuration" :track="item" />
          <CellActions :track="item">
            <slot name="context-menu" :index="tracks.indexOf(item)" :item="item" />
          </CellActions>
        </tr>
      </template>
    </tbody>
  </BaseTable>
</template>
<script lang="ts">
  import { defineComponent, PropType } from 'vue'
  import CellDuration from '@/library/track/CellDuration.vue'
  import CellArtist from '@/library/track/CellArtist.vue'
  import CellAlbum from '@/library/track/CellAlbum.vue'
  import CellTrackNumber from '@/library/track/CellTrackNumber.vue'
  import CellActions from '@/library/track/CellActions.vue'
  import CellTitle from '@/library/track/CellTitle.vue'
  import BaseTable from '@/library/track/BaseTable.vue'
  import BaseTableHead from '@/library/track/BaseTableHead.vue'
  import { Album, Track } from '@/shared/api'
  import { usePlayerStore } from '@/player/store'

  export default defineComponent({
    components: {
      BaseTableHead,
      BaseTable,
      CellTitle,
      CellActions,
      CellTrackNumber,
      CellAlbum,
      CellArtist,
      CellDuration,
    },
    props: {
      album: { type: Object as PropType<Album>, required: true },
      tracks: { type: Array as PropType<Track[]>, required: true },
      noAlbum: { type: Boolean, default: false },
      noArtist: { type: Boolean, default: false },
      noDuration: { type: Boolean, default: false },
    },
    setup() {
      return {
        playerStore: usePlayerStore(),
      }
    },
    computed: {
      isPlaying(): boolean {
        return this.playerStore.isPlaying
      },
      playingTrackId() {
        return this.playerStore.trackId
      },
      groupedTracks() {
        return Object.groupBy(this.tracks, track => track.discNumber ?? 0)
      },
      hasMultipleDiscs(): boolean {
        return Object.keys(this.groupedTracks).length > 1
      }
    },
    methods: {
      play(index: number) {
        if (this.tracks[index].id === this.playingTrackId) {
          return this.playerStore.playPause()
        }
        return this.playerStore.playTrackList(this.tracks, index)
      },
      dragstart(item: any, event: any) {
        if (!item.isStream) {
          event.dataTransfer.setData('application/x-track-id', item.id)
        }
      },
    }
  })
</script>
