<template>
  <Tiles square :allow-h-scroll="allowHScroll">
    <Tile
      v-for="item in items" :key="item.id"
      :to="{name: 'album', params: { id: item.id } }"
      :title="item.name"
      :image="item.image"
      :draggable="true" @dragstart="dragstart(item.id, $event)"
    >
      <template #text>
        <slot name="text" v-bind="item">
          <ArtistLinks :artists="item.artists" :display-artist="item.displayArtist" link-class="text-muted" />
        </slot>
      </template>
      <template #context-menu>
        <DropdownItem icon="play" @click="playNow(item.id)">
          Play
        </DropdownItem>
        <DropdownItem icon="plus" @click="playNext(item.id)">
          Play next
        </DropdownItem>
        <DropdownItem icon="plus" @click="playLater(item.id)">
          Add to queue
        </DropdownItem>
        <DropdownItem
          :icon="favourites[item.id] ? 'heart-fill' : 'heart'"
          @click="toggleFavourite(item.id)"
        >
          Favourite
        </DropdownItem>
      </template>
    </Tile>
  </Tiles>
</template>
<script lang="ts">
  import { defineComponent } from 'vue'
  import { useFavouriteStore } from '@/library/favourite/store'
  import { usePlayerStore } from '@/player/store'
  import ArtistLinks from '@/library/artist/ArtistLinks.vue'

  export default defineComponent({
    components: { ArtistLinks },
    props: {
      items: { type: Array, required: true },
      allowHScroll: { type: Boolean, default: false },
    },
    setup() {
      return {
        favouriteStore: useFavouriteStore(),
        playerStore: usePlayerStore(),
      }
    },
    computed: {
      favourites(): any {
        return this.favouriteStore.albums
      },
    },
    methods: {
      async playNow(id: string) {
        const album = await this.$api.getAlbumDetails(id)
        return this.playerStore.playTrackList(album.tracks!)
      },
      async playNext(id: string) {
        const album = await this.$api.getAlbumDetails(id)
        return this.playerStore.setNextInQueue(album.tracks!)
      },
      async playLater(id: string) {
        const album = await this.$api.getAlbumDetails(id)
        return this.playerStore.addToQueue(album.tracks!)
      },
      toggleFavourite(id: string) {
        return this.favouriteStore.toggle('album', id)
      },
      dragstart(id: string, event: any) {
        event.dataTransfer.setData('application/x-album-id', id)
      },
    }
  })
</script>
