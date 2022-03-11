<script lang="ts" setup>
import hotkeys from 'hotkeys-js';

const time = ref(0);
const score = ref(0);
const colorCenter = ref('blue');
const colorRight = ref('red');
const colorLeft = ref('green');

let tm;

function getRandomColors(){
    const colors = ['blue','red','green'];
    const c1 =  colors.splice(Math.floor(Math.random() * colors.length), 1)[0];
    const c2 =  colors.splice(Math.floor(Math.random() * colors.length), 1)[0];
    const c3 =  colors.splice(Math.floor(Math.random() * colors.length), 1)[0];
    return [c1, c2, c3];
}

function action(target) {
    if(time.value <= 0){
        return false
    }
    if(target.value === colorCenter.value){
        const [c1, c2] = getRandomColors();
        score.value += 1;
        colorLeft.value = c1;
        colorRight.value = c2;
        colorCenter.value = Math.floor(Math.random() * 100) % 2 === 0 ? c1 : c2;
    }
    else{
        score.value -= 1;
    }

    return false;
}

function reset() {
    score.value = 0;
    const [c1, c2] = getRandomColors();
    colorLeft.value = c1;
    colorRight.value = c2;
    colorCenter.value = Math.random() % 2 === 0 ? c1 : c2;
    time.value = 10;
    clearInterval(tm);
    tm = setInterval(()=>{
        time.value--;
        if(time.value <= 0){
            clearInterval(tm);
        }
    }, 1000);

}

onMounted(() => {
    reset()
    hotkeys('left',  action.bind(null, colorLeft));
    hotkeys('right',  action.bind(null, colorRight));
})

onUnmounted(() => {
    hotkeys.unbind('left');
    hotkeys.unbind('right');
    clearInterval(tm);
})
</script>

<template>
    <div>
        <div class="main">
            <div class="sides box left" :style="{backgroundColor: colorLeft}"></div>
            <div class="box center" :style="{backgroundColor: colorCenter}"></div>
            <div class="sides box right" :style="{backgroundColor: colorRight}"></div>
        </div>
        <div>Scores: {{score}}</div>

        <div>
            Time: {{ time }}
        </div>

        <button v-if="time <= 0" @click="reset">
            reset
        </button>
    </div>
</template>

<style>
.main{
    display: flex;
}
.center{
    margin: 6px;
    height: 80px;
    width: 80px;
}
.left{

}
.right{
    border: 3px solid;
}
.box{
    border: 3px solid;
    border-radius: 10%;
}
.sides{
    height: 100px;
    width: 100px;
}
</style>