<template>
  <div class="list-music" v-if="!isPlayerVisiable">
    <div  v-for="(music , index) in lists" :key="music.id" @click="playmusic(index)" class="d-flex justify-content-between mb-3">
      <div>
        <span>{{music.name}}</span>
        <br/>
        <span> {{music.artistName}} - {{music.albumName}} - {{music.year}}</span>
      </div>
      <div>
        <img :src=music.src class="img-music">
      </div>
    </div>
  </div>


  <div v-else>
    <music-player :listMusic="lists[currentindex]" @isPlayerVisiable="changevisiable" @next="playnext" @previous="playprevious" @getfavorite="handleFavorite"></music-player>
  </div>
</template>

<script setup>
import MusicPlayer from "@/components/MusicPlayer.vue";
import {reactive, ref} from "vue";

const currentindex = ref(0)
const isPlayerVisiable = ref(true)
const lists = reactive([
  {
    id: 1,
    name: 'ba to ghashange',
    artistName: 'HoroshBand',
    albumName: 'single',
    year: 2021,
    src: `https://dl.pop-music.ir/images/1402/Tir/Hoorosh-Mehdi-Darabi-Bato-Ghashangeh.jpg`,
    songSrc: `https://dl.pop-music.ir/music/1402/Tir/Hoorosh-Mehdi%20Darabi%20-%20Bato%20Ghashangeh%20(128).mp3`,
    favorite: false,

  },
  {
    id: 2,
    name: 'yeki bod yeki nabod',
    artistName: 'Sina Shabankhani',
    albumName: 'single',
    year: 2001,
    src: `https://dl.pop-music.ir/images/1402/Tir/Sina-Shabankhani-Yeki-Bood-Yeki-Nabood.jpg`,
    songSrc: `https://dl.pop-music.ir/music/1402/Tir/Sina%20Shabankhani%20-%20Yeki%20Bood%20Yeki%20Nabood%20(128).mp3`,
    favorite: true,

  },
  {
    id: 3,
    name: 'ghayegh chobi',
    artistName: 'Mordad Mahor',
    albumName: 'single',
    year: 2013,
    src: `https://dl.pop-music.ir/images/1402/Mordad/Mahoor-Ghayeghe-Choobi.jpg`,
    songSrc: `https://dl.pop-music.ir/music/1402/Mordad/Mahoor%20-%20Ghayeghe%20Choobi%20(128).mp3`,
    favorite: false,

  }
])

function changevisiable(){
  isPlayerVisiable.value = !isPlayerVisiable.value
}
function playmusic(index){
  currentindex.value = index
  isPlayerVisiable.value = !isPlayerVisiable.value

}
function playnext(){

  if (currentindex.value < lists.length - 1 ){
    currentindex.value++
    console.log(currentindex.value)
  }else {
    currentindex.value =0
  }
}
function playprevious(){
  if(currentindex.value != 0){
    currentindex.value--
    console.log(currentindex.value)

  }else {
    currentindex.value = lists.length - 1
    console.log(currentindex.value)
  }
}
function handleFavorite(){
  lists[currentindex.value].favorite = !lists[currentindex.value].favorite
}
</script>

<style scoped>
.list-music {
  background-image: linear-gradient(45deg, rgb(223, 217, 255) 0%, rgb(174, 184, 228) 100%);
  height: 100%;
  padding: 15px;
}
.img-music {
  width: 60px;
  border-radius: 10px;
}
</style>
