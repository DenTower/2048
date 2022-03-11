<script lang="ts" setup>
const score = ref(0);
const scorePC = ref(0);
const message = ref('');
const end = ref(false);

let cards = [];

function initCards() {
    cards = [];
    for(let i = 0, j = 6, t = 0; i < 36; i+=4, j++){
        if(j === 14){
            t = 11;
        }
        else if(j > 10) {
            t = 10;
        }
        else{
            t = j;
        }
        cards.push(t, t, t, t);
    }
}

function takeCard() {
    if(end.value){
        return;
    }
    const index = Math.floor(Math.random() * cards.length);
    const card = cards.splice(index, 1)[0];

        score.value +=
            card === 11
                ? (score.value + 11 > 21 ? 1 : 11)
                : card;

    console.log(cards)
    if(score.value > 21) {
        message.value = 'lose';
        end.value = true;
    }
    if(score.value === 21) {
        message.value = 'win';
        end.value = true;
        done()
    }
}

function done() {
    for(let i = 0; i <= 100; i++) {
        const index = Math.floor(Math.random() * cards.length);
        const card = cards.splice(index, 1)[0];
        if(card === 11){
            if(scorePC.value + 11 > 21){
                scorePC.value += 1;
            }
            else{
                scorePC.value += 11;
            }
        } else {
            scorePC.value += card;
        }
        if(scorePC.value >= 21) {
            break;
        }
    }
    if(score.value > scorePC.value || scorePC.value > 21){
        message.value = 'win';
    }
    else if(score.value === scorePC.value) {
        message.value = 'draw';
    }
    else{
        message.value = 'lose';
    }
    end.value = true;
}

function restart(){
    score.value = 0;
    scorePC.value = 0;
    end.value = false
    message.value = '';
    initCards()
}

initCards();
</script>

<template>

    <div>
        {{ message }}
        {{ score }} - {{scorePC}}
        <button  v-if="!end" @click="takeCard">
        take card
        </button>
        <button  v-if="!end" @click="done">
            enough
        </button>
        <button v-if="end" @click="restart">
            restart
        </button>
    </div>
</template>

<style>
.test {
    color:red;
}
</style>