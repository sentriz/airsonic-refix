<template>
  <div v-if="album">
    <Hero :image="album.image">
      <h1>
        {{ album.name }}
        <b-button variant="link" class="p-0" @click="toggleFavourite">
          <Icon :icon="isFavourite ? 'heart-fill' : 'heart'" />
        </b-button>
      </h1>
      <p>
        by
        <template v-for="(artist, index) in album.artists">
          <span v-if="index > 0" :key="artist.id" class="text-muted">, </span>
          <router-link :key="`${artist.id}-link`" :to="{name: 'artist', params: { id: artist.id }}">{{ artist.name }}</router-link>
        </template>
        <span v-if="album.year"> • {{ album.year }}</span>
        <span v-if="duration"> • {{ duration }}</span>
        <span v-if="album.genres.length"> •
          <template v-for="({ name: genre }, index) in album.genres">
            <span v-if="index > 0" :key="genre" class="text-muted">, </span>
            <router-link :key="`${genre}-link`" :to="{name: 'genre', params: { id: genre }}">{{ genre }}</router-link>
          </template>
        </span>
      </p>
      <OverflowFade class="mb-3">
        {{ album.description }}
        <div class="mt-1 links">
          <ExternalLink v-if="album.lastFmUrl" :href="album.lastFmUrl">
            Last.fm <Icon icon="link" />
          </ExternalLink>
          <ExternalLink v-if="album.musicBrainzUrl" :href="album.musicBrainzUrl">
            MusicBrainz <Icon icon="link" />
          </ExternalLink>
          <ExternalLink :href="`https://rateyourmusic.com/search?searchterm=${encodeURIComponent(album.name)}&searchtype=l`">
            Rate Your Music <Icon icon="link" />
          </ExternalLink>
        </div>
      </OverflowFade>
      <div class="text-nowrap">
        <b-button variant="secondary" class="mr-2" @click="playNow">
          <Icon icon="play" /> Play
        </b-button>
        <b-button variant="secondary" class="mr-2" @click="shuffleNow">
          <Icon icon="shuffle" /> Shuffle
        </b-button>
        <OverflowMenu class="px-1">
          <ContextMenuItem icon="plus" @click="setNextInQueue">
            Play next
          </ContextMenuItem>
          <ContextMenuItem icon="plus" @click="addToQueue">
            Add to queue
          </ContextMenuItem>
        </OverflowMenu>
      </div>
    </Hero>
    <div class="row">
      <div class="col">
        <TrackList :tracks="album.tracks" no-album />
      </div>
    </div>
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue'
  import TrackList from '@/library/track/TrackList.vue'
  import { Album } from '@/shared/api'
  import { useFavouriteStore } from '@/library/favourite/store'
  import { formatDuration } from '@/shared/utils'
  import OverflowFade from '@/shared/components/OverflowFade.vue'

  export default defineComponent({
    components: {
      TrackList,
      OverflowFade,
    },
    props: {
      id: { type: String, required: true }
    },
    setup() {
      return {
        favouriteStore: useFavouriteStore()
      }
    },
    data() {
      return {
        album: null as null | Album,
      }
    },
    computed: {
      isFavourite(): boolean {
        return !!this.favouriteStore.albums[this.id]
      },
      duration(): string {
        if (!this.album?.duration) return ''
        return formatDuration(this.album.duration)
      }
    },
    async created() {
      this.album = await this.$api.getAlbumDetails(this.id)
    },
    methods: {
      playNow() {
        return this.$store.dispatch('player/playNow', {
          tracks: this.album!.tracks,
        })
      },
      shuffleNow() {
        return this.$store.dispatch('player/shuffleNow', {
          tracks: this.album!.tracks,
        })
      },
      setNextInQueue() {
        if (this.album) {
          return this.$store.dispatch('player/setNextInQueue', this.album.tracks)
        }
      },
      addToQueue() {
        if (this.album) {
          return this.$store.dispatch('player/addToQueue', this.album.tracks)
        }
      },
      toggleFavourite() {
        return this.favouriteStore.toggle('album', this.id)
      },
    }
  })
</script>
<style scoped>
  .links {
    display: flex;
    gap: 12px;
  }
</style>
