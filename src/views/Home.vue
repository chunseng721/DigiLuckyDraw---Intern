<template>
  <div class="k-g2ftbq">

    <LuckyDraw @start="start" @playaudio="playaudio" @animationOver="animationOver" :id="prizeId" />

  </div>

</template>

<script lang="ts" setup>
import sound from '../assets/luckdraw/slotmachines.mp4'
import sound2 from '../assets/luckdraw/Reveal.wav'
import LuckyDraw from '@/components/luckydraw.vue'
import { ref } from 'vue'

var audio = new Audio(sound)
var audio2 = new Audio(sound2)
const prizeId = ref<number | string>('') //奖品id,后端获取

const playaudio = () => {
  // if(audio.pause() != null){
  //     audio = new Audio(sound);
  //     audio.play();
  // }else{
  //   audio.play();
  // }
  audio2.pause()
  audio2 = new Audio(sound2)
  audio.play()
  //var audio = new Audio(sound)
  // audio2.pause();
  // audio.play();
}

const start = async () => {
  prizeId.value = ''
  //请求获取中奖id

  setTimeout(() => {
    prizeId.value = Math.floor(Math.random() * 8)
    const prizelist = localStorage.getItem('prize');
    if (prizelist !== null) {
      const prize = JSON.parse(prizelist);
      //console.log(prize)
      while (prize[prizeId.value].qty == 0) {
        prizeId.value = Math.floor(Math.random() * 8)
      }
    }
  })

}

//动画结束 处理对应弹窗
const animationOver = (id: number) => {
  audio.pause();
  audio = new Audio(sound);
  //alert(`中奖id:${id}`)
  // var audio = new Audio(sound2) 
  audio2.play();
  
}

</script>
<style lang="less">
.k-g2ftbq {
  font-size: 16px;
  // background: #a7c9f1;
  background: url('../assets/luckdraw/newbg.png') no-repeat;
  min-height: 100vh;

  box-sizing: border-box;

  background-size: 100% auto;


}
</style>
