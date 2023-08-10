<template>
  <div class="top-music">
    <button @click="goback" class="btn p-0"><img src="@/assets/img/back.svg"></button>
    <div class="text-center info-music">
      <img class="mt-4 mb-3 img-music" :src="listMusic.src"/>
      <p class="text fw-bold">{{ listMusic.name }}</p>
      <p class="text">{{ listMusic.artistName }} - {{ listMusic.albumName }}</p>
    </div>
    <div class="volum-box text-center" :class="{active : volume.active}">
      <span class="volum-down" @click="handleVolumDown">
        <i class="bi-dash"></i>
      </span>
      <div class="volumeProgressBar" @click="changevolume" ref="volumemusic">
        <div class="volumeProgress" :style="{ width: volumeWidth}"></div>
      </div>
      <input type="range" class="volum-range d-none" step="1" :value="volume.range" min="0" max="100" />
      <span class="volum-up"  @click="handleVolumUp">
        <i class="bi-plus"></i>
      </span>
    </div>
    <div class="btn-box text-center">
      <i class="bi-repeat  me-2 fs-4" @click="changeRepet" :class="{active : isLoop}"></i>
      <i class="bi-heart-fill me-2 fs-4"  @click="changeFavorite" :class="{active : listMusic.favorite}"></i>
      <i v-if="volume.range != 0" class="bi-volume-up me-2 fs-4"  :class="{active: volume.active }" @click="handleVolum"></i>
      <i v-else class="bi-volume-mute fs-4"></i>
    </div>
    <div class="music-box d-flex align-items-center justify-content-around">
      <span class="current-time">{{ seekbar.currentTime }}</span>
      <div class="currentProgressBar" @click="clickProgress" ref="progress">
        <div class="currentProgress" :style="{ width: barWidth}"></div>
      </div>
      <input type="range" id="progress-bar" :max="seekbar.max" step="1" class="seekbar d-none" min="0"
             v-model="seekbar.value">
      <audio class="d-none" controls :src="listMusic.songSrc" preload="auto"  ref="audioplayer">audio</audio>
      <span class="duration">{{ seekbar.duration }}</span>
    </div>
  </div>
  <div class="bottom-music">
    <div class="d-flex justify-content-between align-items-center action-music">
      <div>
        <button class="btn" @click="previous"><img src="@/assets/img/previous.svg"/></button>
      </div>
      <div>
        <button class="play-btn" @click="toggleplaymusic">
          <img v-if="isplaying" src="@/assets/img/pause.svg">
          <img v-else src="@/assets/img/play.svg">
          <!--        {{isplaying ? 'pause' : 'play' }}-->
        </button>
      </div>
      <div>
        <button class="btn" @click="next"><img src="@/assets/img/next.svg"/></button>
      </div>
    </div>
  </div>
</template>

<script setup>
import {defineEmits, defineProps, onMounted, reactive, ref} from "vue";


const props = defineProps(['listMusic'])
const emit = defineEmits(['isPlayerVisiable', 'next', 'previous' , 'getfavorite'])
console.log(props, emit)
const progress = ref(null)
const volumemusic = ref(null)
const isplaying = ref(false)
const audioplayer = ref(null)
const isLoop = ref(false)
const volume = reactive({
  active : false,
  range: 80
})
const seekbar = reactive({
  max: 100,
  value: 0,
  duration: '0:00',
  currentTime: '0:00'

})

const width = ref()
const barWidth = ref(null)
const volumeWidth = ref(null)
const durmin = ref()
const dursec = ref()
const curmin = ref()
const cursec = ref()


function goback() {
  emit('isPlayerVisiable')
}
function resetPlayer() {
  barWidth.value = 0;
  audioplayer.value.currentTime = 0;



  setTimeout(() => {
    if(isplaying.value) {
      audioplayer.value.play();
    } else {
      audioplayer.value.pause();
    }
  }, 300);
}
function next() {
  emit('next')
  resetPlayer()

}
function previous() {
  emit('previous')
  resetPlayer()
}
function toggleplaymusic() {

  if (audioplayer.value.paused) {
    audioplayer.value.play()
    isplaying.value = true

  } else {
    audioplayer.value.pause()
    isplaying.value = false

  }
}
function generateTime() {
  setTimeout(() => {
    width.value = (100 / audioplayer.value.duration) * audioplayer.value.currentTime
    // console.log(width.value)
    // console.log(Math.round(width.value))

    barWidth.value = width.value + "%";

    durmin.value = Math.floor(audioplayer.value.duration / 60);
    // console.log(durmin.value)

    dursec.value = Math.floor(audioplayer.value.duration - durmin.value * 60);
    // console.log(dursec.value)

    curmin.value = Math.floor(audioplayer.value.currentTime / 60);
    // console.log(curmin.value)
    cursec.value = Math.floor(audioplayer.value.currentTime - curmin.value * 60);
    // console.log(cursec.value)

    if (durmin.value < 10) {
      durmin.value = "0" + durmin.value;
    }
    if (dursec.value < 10) {
      dursec.value = "0" + dursec.value;
    }
    if (curmin.value < 10) {
      curmin.value = "0" + curmin.value;
    }
    if (cursec.value < 10) {
      cursec.value = "0" + cursec.value;
    }
    // trackDuration.value = Math.round(seekbar.duration)

    seekbar.duration = durmin.value + ":" + dursec.value;
    seekbar.currentTime = curmin.value + ":" + cursec.value;

    // currentProgressBar.value = audioplayer.value.currentTime / trackDuration.value * 100;

  }, 1000)


}
function clickProgress(e) {


  console.log(audioplayer.value.duration)
  console.log(e)
  console.log(e.pageX)
  console.log(progress.value.offsetLeft)
  let position = e.pageX - progress.value.offsetLeft
  console.log(position)
  console.log(progress.value.offsetWidth)
  let percentage = (100 * position) / progress.value.offsetWidth
  console.log(percentage)
  if (percentage > 100) {
    percentage = 100;
  }
  if (percentage < 0) {
    percentage = 0;
  }
  barWidth.value = percentage + "%";
  audioplayer.value.currentTime = (audioplayer.value.duration * percentage) / 100;
  console.log(audioplayer.value.currentTime)
  audioplayer.value.play();
  isplaying.value = true

}

function changevolume(e){
  console.log(e.pageX)
  console.log(volumemusic.value.offsetLeft)
  let position = e.pageX - volumemusic.value.offsetLeft
  console.log(position)
  let percentage = (100 * position) / volumemusic.value.offsetWidth
  console.log(percentage)
  if (percentage > 100) {
    percentage = 100;
  }
  if (percentage < 0) {
    percentage = 0;
  }
  if (position > 100) {
    position = 100;
  }
  if (position < 0) {
    position = 0;
  }
  volumeWidth.value = position + '%';
  audioplayer.value.volume = percentage / 100
  volume.range = position


}
function handleVolum(){
  volume.active = !volume.active
}
function handleVolumDown() {
  if (volume.range > 20 || volumeWidth.value > 0) {
    volume.range = Number(volume.range) - 20
    audioplayer.value.volume = (volume.range) / 100
    console.log(audioplayer.value.volume)
    console.log(volume.range)
    volumeWidth.value = volume.range  + '%';
  }
}
function handleVolumUp() {
  if (volume.range < 80 || volumeWidth.value < 100) {
    volume.range = Number(volume.range) + 20
    audioplayer.value.volume = Number(volume.range) / 100
    console.log(audioplayer.value.volume)
    volumeWidth.value = volume.range + '%';
  }
}
function changeRepet(){
  audioplayer.value.loop = !audioplayer.value.loop
  isLoop.value = !isLoop.value

}
function changeFavorite() {
  emit('getfavorite')
}

onMounted(() => {
  audioplayer.value.ontimeupdate = function () {
    generateTime()
  }
  audioplayer.value.onloadmetadata = function () {
    generateTime()
  }

  audioplayer.value.onended = function () {
    console.log('endtime')
    audioplayer.value.currentTime = 0
    isplaying.value = false
  }
})

</script>

<style lang="scss" scoped>
.top-music {
  background-image: linear-gradient(45deg, rgb(223, 217, 255) 0%, rgb(174, 184, 228) 100%);
  padding: 15px;
}
.bottom-music {
  border: 3px solid #ddd7f3;
  padding: 15px;
}
.volumeProgressBar {
  width: 100%;
  background-color: rgba(0, 0, 0, 0.1);
  margin: 0.75rem 0;
  border-radius: 15px;
  cursor: pointer;

  .volumeProgress {
    background-color: blueviolet;
    //width: 0px;
    height: 5px;
    border-radius: 15px;
    transition: 100ms;

  }
}
.currentProgressBar {
  width: 100%;
  background-color: rgba(0, 0, 0, 0.1);
  margin: 0.75rem 0;
  border-radius: 15px;
  cursor: pointer;

  .currentProgress {
    background-color: blueviolet;
    width: 0px;
    height: 5px;
    border-radius: 15px;
    transition: 100ms;
  }
}

.current-time, .duration {
  margin: 0 8px;
}

.volum-box {
  display: none;
  width: 50%;
  margin: 0 auto;
  &.active {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
}

.bi-plus , .bi-dash {
  cursor: pointer;
}
.bi-heart-fill {
  &.active {
    color: red;
  }
}

.bi-repeat {
  &.active {
    color: #472EB4;
  }
}
.img-music {
  width: 250px;
  height: 250px;
  border-radius: 60px;
  box-shadow: rgba(71, 46, 180, 100) 0px 3px 8px;;
}

.action-music {
  img {
    width: 35px;
  }
  .play-btn {
    border-radius: 50%;
    background-color: #fff;
    border: 1px solid #472EB4;
    width: 65px;
    height: 65px;
    img {
      width: 45px;
      height: 45px;
    }
  }
}
.info-music {
  .text {
    color: #333131;
    margin-bottom: 10px;
  }
}

input[type="range"] {
  height: 8px;
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  cursor: pointer;
  outline: none;
  overflow: hidden;
  border-radius: 16px;
}

input[type="range"]::-webkit-slider-runnable-track {
  height: 8px;
  background: #ccc;
  border-radius: 16px;
}

input[type="range"]::-moz-range-track {
  height: 1px;
  background: #ccc;
  border-radius: 16px;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  height: 8px;
  width: 8px;
  background-color: #fff;
  border-radius: 50%;
  border: 2px solid #472EB4;
  box-shadow: -407px 0 0 400px #472EB4;
}
.range {
  display: flex;
  align-items: center;
  max-width: 500px;
  height: 1rem;
  width: 80%;
  background: #fff;
  padding: 0px 10px;
}
</style>

<!--هندل کردن استایل پروژه* -->
<!--هندل کردن صدا در پروژه* -->
<!--هندل کردن علاقه مندی ها در پروژه* -->
<!--افزودن مواردی که گفت به پروژه اب و هوا -->
<!--تمرین و دیدن مجدد فیلم های vue-x-->
<!-- - صدا با کم و زیاد کردن پروگرس بار هم باید کم و زیادبشه  -->
