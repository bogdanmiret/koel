<template>
  <article
    :class="{ playing: song.playback_state === 'Playing' || song.playback_state === 'Paused' }"
    data-testid="song-card"
    draggable="true"
    tabindex="0"
    @dragstart="onDragStart"
    @contextmenu.prevent="requestContextMenu"
    @dblclick.prevent="play"
  >
    <SongThumbnail :song="song"/>
    <main>
      <div class="details">
        <h3>{{ song.title }}</h3>
        <p class="by text-secondary">
          <a :href="`#/artist/${song.artist_id}`">{{ song.artist_name }}</a>
          - {{ pluralize(song.play_count, 'play') }}
        </p>
      </div>
      <LikeButton :song="song"/>
    </main>
  </article>
</template>

<script lang="ts" setup>
import { toRefs } from 'vue'
import { eventBus, pluralize } from '@/utils'
import { queueStore } from '@/stores'
import { playbackService } from '@/services'
import { useDraggable } from '@/composables'

import SongThumbnail from '@/components/song/SongThumbnail.vue'
import LikeButton from '@/components/song/SongLikeButton.vue'

const props = defineProps<{ song: Song }>()
const { song } = toRefs(props)

const { startDragging } = useDraggable('songs')

const requestContextMenu = (event: MouseEvent) => eventBus.emit('SONG_CONTEXT_MENU_REQUESTED', event, song.value)
const onDragStart = (event: DragEvent) => startDragging(event, [song.value])

const play = () => {
  queueStore.queueIfNotQueued(song.value)
  playbackService.play(song.value)
}
</script>

<style lang="scss" scoped>
article {
  display: flex;
  gap: 12px;
  padding: 8px 12px 8px 8px;
  background: var(--color-bg-secondary);
  border: 1px solid var(--color-bg-secondary);
  border-radius: 5px;
  align-items: center;

  &:focus, &:focus-within {
    box-shadow: 0 0 1px 1px var(--color-accent);
  }

  &.playing {
    color: var(--color-accent);
  }

  button {
    color: var(--color-text-secondary);
    opacity: 0;
  }

  &:hover {
    button {
      opacity: 1;
    }
  }

  @media (hover: none) {
    button {
      opacity: 1;
    }

    ::v-deep(.cover) {
      .control {
        display: flex;
      }

      &::before {
        opacity: .7;
      }
    }
  }

  // show the thumbnail's playback control on the whole card focus and hover
  &:hover ::v-deep(.cover), &:focus ::v-deep(.cover) {
    .control {
      display: flex;
    }

    &::before {
      opacity: .7;
    }
  }

  main {
    flex: 1 1 auto;
    min-width: 0;
    display: flex;
    align-items: flex-start;
    gap: 8px;

    .play-count {
      background: rgba(255, 255, 255, 0.08);
      position: absolute;
      height: 100%;
      top: 0;
      left: 0;
      pointer-events: none;
    }

    .by {
      font-size: .9rem;
      opacity: .8;

      a {
        color: var(--color-text-primary);

        &:hover {
          color: var(--color-accent);
        }
      }
    }

    .details {
      flex: 1;
      display: flex;
      flex-direction: column;
      gap: 4px;
      overflow: hidden;
    }
  }

  h3 {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    width: 100%;
  }
}
</style>
