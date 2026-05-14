<template>
  <span>
    <template v-for="(part, i) in parts">
      <router-link v-if="part.value" :key="i"
                   :to="{name: 'artist', params: { id: part.value.id }}"
                   :class="linkClass"
                   @click.native.stop>{{ part.text }}</router-link>
      <span v-else :key="i" class="text-muted">{{ part.text }}</span>
    </template>
  </span>
</template>
<script lang="ts">
  import { defineComponent, PropType } from 'vue'
  import { splitByLabels, LabeledToken } from '@/shared/utils'

  type Artist = { id: string, name: string }

  export default defineComponent({
    props: {
      artists: { type: Array as PropType<Artist[]>, required: true },
      displayArtist: { type: String, default: '' },
      linkClass: { type: String, default: '' },
    },
    computed: {
      parts(): LabeledToken<Artist>[] {
        const tokens = splitByLabels(this.displayArtist, this.artists, a => a.name)
        const matched = new Set(tokens.filter(t => t.value).map(t => t.text))
        const expected = new Set(this.artists.map(a => a.name).filter(Boolean)).size
        if (matched.size === expected) return tokens
        return this.commaSeparated()
      }
    },
    methods: {
      commaSeparated(): LabeledToken<Artist>[] {
        const out: LabeledToken<Artist>[] = []
        this.artists.forEach((a, i) => {
          if (i > 0) out.push({ text: ', ' })
          out.push({ text: a.name, value: a.id ? a : undefined })
        })
        return out
      }
    }
  })
</script>
