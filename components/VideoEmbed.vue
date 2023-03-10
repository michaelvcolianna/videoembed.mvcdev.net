<script setup>
const props = defineProps({
  videoId: String,
  cardSrc: {
    type: String,
    default: null
  }
})

// @note Made these refs in case any dynamic video loading is needed
const card = ref(null)
const player = ref(null)

// @note Using YouTube explicitly here; ideally a method to determine the source
//       would be best for making the iframe and card fallback srcs
const iframeCard = cardSrc ?? `https://i.ytimg.com/vi/${props.videoId}/hqdefault.jpg`
const iframeSrc = `https://www.youtube.com/embed/${props.videoId}?&iv_load_policy=3&modestbranding=1&playsinline=1&showinfo=0&rel=0&enablejsapi=1`
const iframeId = `video-${props.videoId}`

/**
 * Handler to hide the card and start the video.
 *
 * @return void
 */
function playVideo() {
  card.value.classList.add('hidden')
  player.value.plyr.play()
}

// Use a promise to load and init the player
onMounted(async () => {
  const Plyr = await import('plyr')

  new Plyr.default(`#${iframeId}`)
})
</script>

<template>
  <div class="video-embed">
    <div class="plyr__video-embed" :id="iframeId">
      <div class="card" ref="card">
        <img
          :src="iframeCard"
          alt=""
          height="720"
          width="1280"
        />

        <button type="button" @click="playVideo">
          <span>Play</span>
        </button>
      </div>

      <iframe
        ref="player"
        :src="iframeSrc"
        width="560"
        height="315"
        allow="accelerometer; clipboard-write; encrypted-media; gyroscope; web-share"
        allowfullscreen
        allowtransparency
      ></iframe>
    </div>
  </div>
</template>

<style scoped>
.video-embed {
  aspect-ratio: 16/9;
  width: 50%;
}

.card {
  align-items: center;
  background-color: rgba(0, 128, 0, 0.50);
  display: grid;
  grid-template-areas: "card";
  height: 100%;
  justify-items: center;
  left: 0;
  position: absolute;
  top: 0;
  width: 100%;
  z-index: 3;
}

.card.hidden {
  z-index: -1;
}

.card > * {
  grid-area: card;
}

.card img {
  height: 100%;
  object-fit: cover;
  width: 100%;
}

.card button {
  align-items: center;
  appearance: none;
  background-color: red;
  border: 0;
  border-radius: 64px;
  color: white;
  cursor: pointer;
  display: flex;
  height: 64px;
  justify-content: center;
  padding: 0;
  text-transform: uppercase;
  width: 64px;
}

.card button:hover {
  transform: scale(1.1);
}
</style>
