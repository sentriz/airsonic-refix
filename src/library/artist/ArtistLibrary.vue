<template>
  <div>
    <ul class="nav-underlined mb-3">
      <li v-for="{ value, text } in options" :key="value">
        <router-link :to="{... $route, params: {... $route.params, sort: value }}">
          {{ text }}
        </router-link>
      </li>
    </ul>
    <ArtistList :items="artists" />
    <InfiniteLoader :key="sort" :loading="loading" :has-more="hasMore" @load-more="loadMore" />
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue'
  import ArtistList from './ArtistList.vue'
  import { Artist, ArtistSort } from '@/shared/api'

  export default defineComponent({
    components: {
      ArtistList,
    },
    props: {
      sort: { type: String, required: true },
    },
    data() {
      return {
        artists: [] as | Artist[],
        loading: false,
        offset: 0 as number,
        hasMore: true,
      }
    },
    computed: {
      options() {
        return [
          { text: 'Most albums', value: 'most-albums' },
          { text: 'A-Z', value: 'a-z' },
        ]
      }
    },
    watch: {
      sort: {
        handler() {
          this.artists = []
          this.offset = 0
          this.hasMore = true
        }
      }
    },
    methods: {
      loadMore() {
        this.loading = true
        return this.$api.getArtists(this.sort as ArtistSort, 100, this.offset).then(artists => {
          this.artists.push(...artists)
          this.offset += artists.length
          this.hasMore = artists.length > 0
          this.loading = false
        })
      }
    }
  })
</script>
