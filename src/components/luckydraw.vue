<template>
    <div @keydown='start' tabindex="0" class="luckdraw">
        <div class="luckdraw_top">
            <img src="" alt="">
        </div>

        <div class="luckdraw_cont">
            <!-- <div class="dot" v-for="(item, index) in dots" :key="index" :class="{ isred: item.isred }"
                    :style="`top:${item.top}vw;left:${item.left}vw`"></div> -->
            <p class="luckdraw_overtime"></p>
            <div class="luckdraw_list">

                <div class="row">
                    <div class="row_gift" :class="{ row_gift_isactive: item.active }" v-for="item in list.slice(0, 3)"
                        :key="item.id">
                        <img :src="item.img" alt="">
                    </div>

                </div>
                <div class="row">
                    <div class="row_gift" :class="{ row_gift_isactive: item.active }" v-for="item in list.slice(3, 5)"
                        :key="item.id">
                        <img :src="item.img" alt="">
                    </div>
                    <div class="row_gift row_start">
                        <img src="../assets/luckdraw/digilogo-01.png" alt="">
                    </div>
                </div>
                <div class="row">
                    <div class="row_gift" :class="{ row_gift_isactive: item.active }" v-for="item in list.slice(5, 8)"
                        :key="item.id">
                        <img :src="item.img" alt="">
                    </div>
                </div>

                <div class="luckdraw_mask" v-if="isMask"></div>
                <div v-if="isOpen">
                    <img id="myImg" alt="Reward" @keydown='start' tabindex="0" />
                </div>

            </div>

            <div class="luckdraw_rest" id="text"></div>
        </div>

    </div>
</template>

<script setup lang="ts">


import gift0 from '../assets/luckdraw/canvas-01.jpg'
import gift1 from '../assets/luckdraw/galaxy fit e-01.jpg'
import gift2 from '../assets/luckdraw/khind-01.jpg'
import gift3 from '../assets/luckdraw/galaxy watch4 44mm-01.jpg'
import gift4 from '../assets/luckdraw/Digi 2 in 1 pillow-01.jpg'
import gift5 from '../assets/luckdraw/kfc-01.jpg'
import gift6 from '../assets/luckdraw/galaxy watch4 42mm-01.jpg'
import gift7 from '../assets/luckdraw/powerbank-01.jpg'
import { ref, watchEffect, defineProps, defineEmits } from 'vue'
const props = defineProps(['id'])
const emits = defineEmits(['start', 'animationOver', 'playaudio'])
interface awardList {
    id: number,
    name: string,
    active: boolean,
    img: string
}

//奖品列表
const list = ref<awardList[]>([
    {
        id: 0,
        name: '5',
        active: true,
        img: gift0
    }, {
        id: 1,
        name: '50',
        active: false,
        img: gift1
    }, {
        id: 2,
        name: '15',
        active: false,
        img: gift2
    }, {
        id: 3,
        name: '20',
        active: false,
        img: gift3
    }, {
        id: 4,
        name: '120',
        active: false,
        img: gift4
    }, {
        id: 5,
        name: 'thanks',
        active: false,
        img: gift5
    }, {
        id: 6,
        name: '25',
        active: false,
        img: gift6
    }, {
        id: 7,
        name: '10',
        active: false,
        img: gift7
    },
])

//抽奖闪烁顺序
const sequenceIds = [0, 1, 2, 4, 7, 6, 5, 3]

const isMask = ref(false); //是否开始抽奖
const isOpen = ref(false);
var spinEnabled = false;
var popOut = false;
const start = () => {
    if (popOut === false) {
        if (spinEnabled === false) {
            isMask.value = true
            isOpen.value = true
            emits('start')
            // var text = document.getElementById("text")
            // if (text !== null) {
            //     if (text.innerHTML == "Congratulations!") {
            //         popOut = true;
            //     }
            // }
            spinEnabled = true
        }
    } else {
        var img = document.getElementById("myImg")
        var text = document.getElementById("text")
        isOpen.value = false
        if (img != null) {
            img.removeAttribute("src")
        }
        if (text != null)
            text.innerHTML = "";
        popOut = false
        spinEnabled = false
    }

    //emits('playaudio')
}

// const close = () => {
//     var img = document.getElementById("myImg")
//     var text = document.getElementById("text")
//     isOpen.value = false
//     if (img != null) {
//         img.removeAttribute("src")
//     }
//     if (text != null)
//         text.innerHTML = "";
// }

/**
 * 抽奖过程动画
 * @param {number} index 
 * @param {number} index 速度
 * @param {number} id 中奖id 
 * @return {void}
 */

let time = 1;

if (localStorage.getItem('prize') === null) {
    let array = [
        { prizeNum: '0', qty: 20 },
        { prizeNum: '1', qty: 0 },
        { prizeNum: '2', qty: 8 },
        { prizeNum: '3', qty: 0 },
        { prizeNum: '4', qty: 1 },
        { prizeNum: '5', qty: 10 },
        { prizeNum: '6', qty: 0 },
        { prizeNum: '7', qty: 0 },];
    localStorage.setItem('prize', JSON.stringify(array))
}


const luckyAnimation = (index = 0, speed = 1, id: number) => {
    emits('playaudio')
    let isfun = false
    time > 10 ? time += 10 : time += 0.5
    time > 50 && (isfun = true)

    setTimeout(() => {
        if (index == 0) {
            list.value[sequenceIds[sequenceIds.length - 1]].active = false
        } else {
            list.value[sequenceIds[index - 1]].active = false
        }

        if (time > 50 && sequenceIds[index] == id)
            emits('playaudio')

        // if(time > 50 && sequenceIds[index] == id)
        //     emits('animationOver', id)


        let array = localStorage.getItem('prize');


        list.value[sequenceIds[index]].active = true
        console.log(sequenceIds[index], id)
        if (isfun && sequenceIds[index] == id) {
            //动画结束,发送弹窗事件
            setTimeout(() => {
                emits('animationOver', id)
                var img = document.getElementById("myImg")
                var text = document.getElementById("text")
                if (img != null) {
                    img.style.display = "block"
                    img.setAttribute("src", list.value[sequenceIds[index]].img)
                    // if (text != null)
                    //     text.innerHTML = "Try again!";
                }


                if (array !== null) {
                    let prize = JSON.parse(array);
                    for (let i = 0; i < prize.length; i++) {
                        if (prize[i].prizeNum == sequenceIds[index]) {
                            if (prize[i].qty > 0) {
                                prize[i].qty = prize[i].qty - 1;
                                localStorage.setItem('prize', JSON.stringify(prize));
                                if (text != null) {
                                    text.innerHTML = "Congratulations!";
                                    popOut = true;
                                }
                            }
                            if (prize[i].qty == 0) {
                                //list.value[sequenceIds[index]].img = 
                            }
                        }
                    }
                }

                isMask.value = false
                list.value[sequenceIds[index]].active = false


            }, 1000)


            time = 1
            isfun = false
            return
        }

        index++
        if (index > 7) index = 0
        luckyAnimation(index, time, id)
    }, 10 * speed)
}

//监听中奖id变化 调用抽奖函数
watchEffect(() => {
    if (sequenceIds.indexOf(props.id) != -1) {
        console.log(123)
        luckyAnimation(0, time, props.id)
    }
})

//边缘闪烁点
interface Dot {
    isred: boolean
    top: number
    left: number
}

const dots = ref<Dot[]>([

])

setInterval(() => {
    dots.value.forEach((item) => {
        item.isred = !item.isred
    })
}, 500)
</script>

<style lang="less">
.luckdraw {

    overflow: hidden;
    padding: 40px;

    .luckdraw_top {
        img {
            width: 350px;
        }

        position: relative;
        top: 30px;
        left: 28%;
    }

    .luckdraw_cont {
        //background: url('../assets/luckdraw/newbg.png') no-repeat;
        background-size: 100% auto;
        // border: 5px black inset;
        // background-color: yellow;
        width: 350px;
        margin: 0 auto;
        // margin-top: 25px;
        // margin-bottom: 20px;
        height: 350px;
        padding-top: 5px;
        position: relative;

        .luckdraw_overtime {
            color: #fff;
            font-size: 16px;
            text-align: center;

        }

        .luckdraw_list {
            width: 85%;
            margin: 0 auto;
            margin-top: -25px;
            position: relative;

            .row {
                display: flex;
                justify-content: space-between;
                position: relative;
                margin-bottom: 20px;
                margin-left: -50px;
                margin-right: -50px;

                .row_gift {
                    width: 124px;
                    height: 110px;
                    border: 1px black inset;
                    box-shadow: 3px 3px rgb(52, 48, 48);

                    img {
                        width: 100%;
                        height: 100%;
                    }
                }

                .row_gift_isactive {
                    position: relative;
                    z-index: 10;
                }

                .row_start {
                    position: absolute;
                    border: none;
                    box-shadow: none;
                    left: calc(80px + (100% - 93*3px) / 2);
                    top: 0;

                }
            }



            .luckdraw_mask {
                position: absolute;
                left: -6.375vw;
                top: 0;
                right: -6.375vw;
                bottom: 0;
                background: #000;
                opacity: 0.5;
                border-radius: 10px;
                overflow: hidden;
                background-size: 100% auto;
            }
        }

        .luckdraw_rest {
            color: rgb(0, 0, 0);
            text-align: center;
            font-weight: bolder;
            // margin-top: 10px;
            margin-right: -3.45vw;
        }
    }
}

.dot {
    width: 8px;
    height: 8px;
    border-radius: 4px;
    background: #FEFE00;
    position: absolute;

}

.isred {
    background: #F97941;
}

#myImg {
    position: absolute;
    top: 0;
    left: -6.3vw;
    // right: 0;
    // bottom: 0;
    width: auto;
    // left: auto;
    height: 100%;
    display: none;
    box-shadow: 3px 3px rgb(52, 48, 48);

}
</style>