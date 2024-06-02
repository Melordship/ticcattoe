<template>
    <div style="width: 100%; height: 100vh; align-items: center; justify-content: center; display: flex; flex-direction: column;">
        <h1>Tic Cat Toe</h1>
        <div class="gameBoard">
            <div class="row row-1">
                <div class="col col-1" @click="onClick('col1')">
                    <img v-if="Object.keys(game).includes('col1')" width="100%" height="100%" :src="images[game['col1']]"/>
                </div>
                <div class="col col-2" @click="onClick('col2')">
                    <img v-if="Object.keys(game).includes('col2')" width="100%" height="100%" :src="images[game['col2']]"/>
                </div>
                <div class="col col-3"  @click="onClick('col3')">
                    <img v-if="Object.keys(game).includes('col3')" width="100%" height="100%" :src="images[game['col3']]"/>
                </div>
            </div>
            <div class="row row-2">
                <div class="col col-1"  @click="onClick('col4')">
                    <img v-if="Object.keys(game).includes('col4')" width="100%" height="100%" :src="images[game['col4']]"/>
                </div>
                <div class="col col-2"  @click="onClick('col5')">
                    <img v-if="Object.keys(game).includes('col5')" width="100%" height="100%" :src="images[game['col5']]"/>
                </div>
                <div class="col col-3"  @click="onClick('col6')">
                    <img v-if="Object.keys(game).includes('col6')" width="100%" height="100%" :src="images[game['col6']]"/></div>
            </div>
            
            <div class="row row-3">
                <div class="col col-1"  @click="onClick('col7')">
                    <img v-if="Object.keys(game).includes('col7')" width="100%" height="100%" :src="images[game['col7']]"/>
                </div>
                <div class="col col-2"  @click="onClick('col8')">
                    <img v-if="Object.keys(game).includes('col8')" width="100%" height="100%" :src="images[game['col8']]"/>
                </div>
                <div class="col col-3"  @click="onClick('col9')">
                    <img v-if="Object.keys(game).includes('col9')" width="100%" height="100%" :src="images[game['col9']]"/>
                </div>
            </div>
        </div>

        <div class="scoreText" style="margin-top: 20px">Player One: {{ playerOneScore }}</div>
        <div class="scoreText">Player Two: {{ playerTwoScore }}</div>
        <div style="margin-top: 30px"><btn class="btn" @click="reset">Reset</btn></div>
    </div>
</template>
<script setup>
import {ref} from "vue";
import _ from "lodash-core";

const images = {
    playerOne: "https://t3.ftcdn.net/jpg/05/78/01/02/360_F_578010240_tGolzJLeqVstZhCkteEyuHch5Oj6Fyyh.jpg",
    playerTwo: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRkg7Rbxs_4PaXnjym3u0B1d86DfZvJXKUBXg&s"
};

const game = ref({});

// const playerOne = "playerOne";
// const playerTwo= "playerTwo";
const columnsSelected = ref([]);

const currentPlayer = ref("playerOne");

const reset = () => {
    columnsSelected.value = [];
    currentPlayer.value = 'playerTwo';
    game.value = {};
}
const onClick = (selected) => {
    if (currentPlayer.value === "playerOne") {
        if (game.value[selected] === 'playerTwo') return;
        game.value[selected] = 'playerOne';
        currentPlayer.value = 'playerTwo';
    } else {
        if (game.value[selected] === 'playerOne') return;
        game.value[selected] = 'playerTwo';
        currentPlayer.value = 'playerOne';
    }
   
    columnsSelected.value = [...columnsSelected.value, selected];

    const val = game.value;
    console.log(Object.entries(val));
    const selectedColsByPlayer = _.groupBy(Object.entries(val).map(([col, player]) => ({col , player})), "player");

    const checkPlayer = (columns) => {
        console.log("columns", columns);
        const matchingPatterns = [
            [1, 2, 3],
            [4, 5, 6],
            [7, 8, 9],
            [1, 5, 9],
            [7, 5, 3],
            [1, 4, 7],
            [2, 5, 8],
            [3, 6, 9]
        ];

        return matchingPatterns.some(pat => {
            return pat.filter(p =>{
                return columns.includes(`col${p}`)
            })?.length === 3;
        })
    }
    const playerOneWin = checkPlayer(selectedColsByPlayer.playerOne?.map(c =>  c.col) || []);
    if (playerOneWin) {
          setTimeout(() => {
            alert('Player one wins');
        }, 500)
        playerOneScore.value++;
    }
    const playerTwoWin = checkPlayer(selectedColsByPlayer.playerTwo?.map(c =>  c.col) || []);
    if (playerTwoWin) {
           setTimeout(() => {
            alert('Player two wins');
        }, 500)
        playerTwoScore.value++;
    }
}

const playerOneScore = ref(0);
const playerTwoScore = ref(0);

</script>

<style>
.gameBoard {
    width: 100%;
    margin-left: 20px;
    margin-right: 20px;
    border-radius: 5%;
    border: 2px solid pink;
    height: 300px;
}

.row {
    display: flex;
    justify-content: space-between;
    gap: 1px;
    height: 33%;
}

.col{
    border: 2px solid pink;
    width: 100%;
}

.btn {
    background: pink;
    border-radius: 5px;
    padding: 10px;
    width: 300px;
    cursor: pointer;
}

.btn:hover {
    filter: brightness(105%);
}

.scoreText {
    font-weight: bold;
    color: purple
}
</style>