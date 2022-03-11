<script lang="ts" setup>
import hotkeys from "hotkeys-js";

const fieldColor = ref('white');
const cells = reactive([]);
const a = Math.floor(Math.random() * 16)
let b = Math.floor(Math.random() * 16)
b = a === b ? (b+1) % 16 : b;

// начальное заполнение массива
for(let i = 0; i < 16; i++){
    cells.push({
        i,
        value: 0,
        color: '',
        op: false
    });
}

cells[a].value = 2;
cells[b].value = 2;
cells[a].color = 'rgb(210,210,210)';
cells[b].color = 'rgb(210,210,210)';

const cellsRows = computed(()=>{
    return cells.reduce((rows, key, index) => (index % 4 == 0 ? rows.push([key])
        : rows[rows.length-1].push(key)) && rows, []);
});

function left() {
    move(-1, 0);
}
function right() {
    move(1, 0);
}
function down() {
    move(0, 1);
}
function up() {
    move(0, -1);
}

function move(x, y) {

    // все цифры можно складывать
    for(let i = 0; i < 16; i++) {
        cells[i].op = false;
    }

    function create() {
        const result = [];
        function direct(i, j) {
                const xy = 4 * i + j;
                const nxy = 4 * (i + y) + (j + x);

                if (
                    (x < 0 && j === 0) || (y < 0 && i === 0) ||
                    (x > 0 && j === 3) || (y > 0 && i === 3) // проверка выхода за границы
                ) {
                    if (cells[xy].value > 0) {

                        result[xy] = cells[xy];

                    }
                    return;
                }

                if (!result[nxy] || result[nxy].value === 0) { // если ячейка не занята (нет элемента) или ячейка пустая
                    result[nxy] = cells[xy]; // если это возможно - перемещаем
                } else if (cells[xy].value === cells[nxy].value && cells[xy].op === false) {
                    result[nxy].value += cells[xy].value;
                    result[nxy].op = true;
                } else {
                    result[xy] = cells[xy];
                }

            }

        if(x > 0 || y > 0) {
            for(let i = 3; i > -1; i--) {
                for (let j = 3; j > -1; j--) {
                    direct(i, j);
                }
            }
        }
        else {
            for(let i = 0; i < 4; i++) {
                for (let j = 0; j < 4; j++) {
                    direct(i, j);
                }
            }
        }

        for(let i = 0; i < 16; i++){  // присваивание значений в основной массив
            cells[i] = result[i] || {
                i,
                value: 0,
                color: '',
                op: false
            }
            cells[i].i = i;
            if(cells[i].value === 2) {
                cells[i].color = 'rgb(210,210,210)';
            }
            else if(cells[i].value === 4) {
                cells[i].color = 'rgb(192,185,155)';
            }
            else if(cells[i].value > 64) {
                let c = 100;
                for(let j = 128; j <= 2048; j += j) {
                    c += -20;

                    if(cells[i].value === j ) { // цвет
                        cells[i].color = 'rgb(255,255,' + c + ')';
                        break;
                    }
                }
            }
            else {
                let g = 200;
                let b = 100;

                for(let j = 8; j <= 64; j += j) {
                    g += -50;
                    b += -25

                    if(cells[i].value === j ) { // цвет
                        cells[i].color = 'rgb(255, ' + g + ', ' + b + ')';
                        break;
                    }
                }
            }
        }
    }

    create();
    create();
    create();
    create();

    // появление новой цифры
    for(let i = 0; i < 16; i++) {
        const a = Math.floor(Math.random() * 16);

        if(cells[a].value === 0){
            cells[a].value = 2;
            cells[a].color = 'rgb(210,210,210)';
            break;
        }
    }
}

onMounted(() => {
    hotkeys('left', left);
    hotkeys('right', right);
    hotkeys('down', down);
    hotkeys('up', up);
})

onUnmounted(() => {
    hotkeys.unbind('left');
    hotkeys.unbind('right');
    hotkeys.unbind('down');
    hotkeys.unbind('up');
})
</script>

<template>
    <div>
        <div class="field" :style="{backgroundColor: fieldColor}">
            <div v-for="(row, index) in cellsRows" :key="index" class="row">
                <div v-for="square in row" :key="'number' + square.i" class="square" :style="{backgroundColor: square.color}">
                    <div v-if="square.value > 0">{{ square.value }}</div>
                </div>
            </div>
        </div>
        <code>
        {{JSON.stringify(cellsRows, null, ' ')}}
        </code>
    </div>
</template>

<style>
.field{
    height: 400px;
    width: 400px;
    border: 5px solid grey;
}
.square{
    height: 98px;
    width: 98px;
    border: 1px solid;
    float:left;
    display: flex;
    align-items: center;
    justify-content: space-evenly;
    font-size: 40px;
}
</style>