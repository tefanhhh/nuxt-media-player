<template>
  <video
    id="player"
    controls
    crossorigin
    playsinline
    poster="https://bitdash-a.akamaihd.net/content/sintel/poster.png"
  ></video>
</template>
<script lang="ts">
import Vue from 'vue'
import Plyr from 'plyr'
import Hls, { Events, Level } from 'hls.js'

export default Vue.extend({
  mounted() {
    const video: HTMLVideoElement = document.querySelector('video')!
    const source =
      'https://bitdash-a.akamaihd.net/content/sintel/hls/playlist.m3u8'
    const options: any = {
      captions: { active: true, update: true, language: 'en' },
      quality: { default: 320, options: [320] },
      controls: [
        'play-large', // The large play button in the center
        'restart', // Restart playback
        'rewind', // Rewind by the seek time (default 10 seconds)
        'play', // Play/pause playback
        'fast-forward', // Fast forward by the seek time (default 10 seconds)
        'progress', // The progress bar and scrubber for playback and buffering
        'current-time', // The current time of playback
        'duration', // The full duration of the media
        'mute', // Toggle mute
        'volume', // Volume control
        'captions', // Toggle captions
        'settings', // Settings menu
        'pip', // Picture-in-picture (currently Safari only)
        'airplay', // Airplay (currently Safari only)
        'download', // Show a download button with a link to either the current source or a custom URL you specify in your options
        'fullscreen', // Toggle fullscreen
      ],
      settings: ['captions', 'quality'],
    }

    if (!Hls.isSupported()) {
      video.src = source
    } else {
      const hls: Hls = new Hls()
      hls.loadSource(source)
      hls.attachMedia(video)

      function updateQuality(newQuality: number) {
        hls.levels.forEach((level: Level, i) => {
          if (level.height === newQuality) {
            console.log('Found quality match with ' + newQuality)
            hls.currentLevel = i
          }
        })
      }

      hls.on(Events.MANIFEST_PARSED, () => {
        const availableQualities: number[] = hls.levels.map((l) => l.height)
        options.quality = {
          default: availableQualities[0],
          options: availableQualities,
          forced: true,
          onChange: (e: number) => updateQuality(e),
        }

        const player: Plyr = new Plyr(video, options)
        player.on('languagechange', () => {
          console.log('jalan')
          setTimeout(() => {
            console.log(hls.subtitleTrack)
            console.log(player.currentTrack)
            hls.subtitleTrack = player.currentTrack
          }, 50)
        })
      })
    }
  },
})
</script>
