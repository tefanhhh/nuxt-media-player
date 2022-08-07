<template>
  <div style="width: 700px">
    <media-player-video />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import mediaPlayer from '~/mixins/media-player'

export default Vue.extend({
  name: 'VideoPage',
  mixins: [mediaPlayer],
})
</script>
