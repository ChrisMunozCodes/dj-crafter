<script setup lang="ts">
import { ref, Ref, watch, onMounted } from 'vue'
import song1 from "../assets/music/song1.mp3"
import song2 from "../assets/music/song2.mp3"
import song3 from "../assets/music/song3.mp3"

const isPlaying = ref(false)
const currentSong = ref(0)

const music = {
  1: {
    title: 'Rising Sun ',
    file: song1
  },
  2: {
    title: 'Happy ',
    file: song2
  },
  3: {
    title: 'Deep Dive ',
    file: song3
  },
}

let playlist = [music[1], music[2], music[3]]

const audioRef: Ref<HTMLAudioElement | null> = ref(null);

function pausePlay() {
  isPlaying.value = !isPlaying.value
  console.log('Pause/Play clicked. Is Playing:', isPlaying.value);

  if (audioRef.value) {
    console.log('Audio element is present');
    if (isPlaying.value) {
      console.log('Attempting to play audio');
      audioRef.value.play().catch(e => console.log('Error playing:', e));
    } else {
      console.log('Attempting to pause audio');
      audioRef.value.pause()
    }
  } else {
    console.log('Audio element is not found');
  }
}

function previousSong() {
  if (currentSong.value > 0) {
    currentSong.value -= 1
  }
  console.log('Previous song, current song index:', currentSong.value);
}

function nextSong() {
  if (currentSong.value < playlist.length - 1) {
    currentSong.value += 1
  }
  console.log('Next song, current song index:', currentSong.value);
}

watch(currentSong, (newSong) => {
  console.log('Current song changed to index:', newSong);
  if (audioRef.value) {
    audioRef.value.src = playlist[newSong].file;
    console.log('Audio source set to:', playlist[newSong].file);
    audioRef.value.load();
    audioRef.value.addEventListener('error', (e) => console.log('Audio error:', e));

    if (isPlaying.value) {
      audioRef.value.play().catch(e => console.log('Error playing:', e));
    }
  }
})

onMounted(() => {
  const player = audioRef.value;
  if (player) {
    player.addEventListener('timeupdate', function() {
      const seekBar = document.getElementById('seekbar') as HTMLProgressElement;
      if (seekBar) {
        seekBar.value = player.currentTime / player.duration * 100;
      }
    });
  }
});
</script>




<template>
<div class="min-h-screen bg-green-400 flex justify-center items-center box-border flex-col">
    <section class="prose">
        <h2>{{ isPlaying ? 'Now Playing ' + playlist[currentSong].title : playlist[currentSong].title + ' is Paused' }}</h2>
    </section>
    <section>
      <audio id="player" ref="audioRef">
        <source :src="playlist[currentSong].file" type="audio/mpeg">
        Your browser does not support the audio element.
      </audio>
      <ul class="flex m-2">
        <li><button class="btn btn-active btn-neutral prose m-2" @click="previousSong"> &lt; </button></li>
        <li><button class="btn btn-active btn-accent prose m-2" @click="pausePlay">{{ isPlaying ? 'Pause' : 'Play' }}</button></li>
        <li><button class="btn btn-active btn-neutral prose m-2" @click="nextSong"> &gt; </button></li>
      </ul>
    </section>
    <section>
        <progress id="seekbar" class="progress progress-green-400 w-56" value="0" max="100"></progress>
    </section>
</div>
  </template>

  

<style scoped>
</style>
