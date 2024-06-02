<template>
    <div style="width: 100%; height: 100vh; align-items: center; justify-content: center; display: flex; flex-direction: column;">
        <h1>Tic Cat Toe</h1>
        <div class="gameBoard">
            <div class="row row-1">
                <div class="col col-1 br bb" @click="onClick('col1')">
                    <img v-if="Object.keys(game).includes('col1')" width="100%" height="100%" :src="images[game['col1']]"/>
                </div>
                <div class="col col-2 br bb" @click="onClick('col2')">
                    <img v-if="Object.keys(game).includes('col2')" width="100%" height="100%" :src="images[game['col2']]"/>
                </div>
                <div class="col col-3 bb"  @click="onClick('col3')">
                    <img v-if="Object.keys(game).includes('col3')" width="100%" height="100%" :src="images[game['col3']]"/>
                </div>
            </div>
            <div class="row row-2">
                <div class="col col-1 br bb"  @click="onClick('col4')">
                    <img v-if="Object.keys(game).includes('col4')" width="100%" height="100%" :src="images[game['col4']]"/>
                </div>
                <div class="col col-2 br bb"  @click="onClick('col5')">
                    <img v-if="Object.keys(game).includes('col5')" width="100%" height="100%" :src="images[game['col5']]"/>
                </div>
                <div class="col col-3 bb"  @click="onClick('col6')">
                    <img v-if="Object.keys(game).includes('col6')" width="100%" height="100%" :src="images[game['col6']]"/></div>
            </div>
            
            <div class="row row-3">
                <div class="col col-1 br"  @click="onClick('col7')">
                    <img v-if="Object.keys(game).includes('col7')" width="100%" height="100%" :src="images[game['col7']]"/>
                </div>
                <div class="col col-2 br"  @click="onClick('col8')">
                    <img v-if="Object.keys(game).includes('col8')" width="100%" height="100%" :src="images[game['col8']]"/>
                </div>
                <div class="col col-3"  @click="onClick('col9')">
                    <img v-if="Object.keys(game).includes('col9')" width="100%" height="100%" :src="images[game['col9']]"/>
                </div>
            </div>
        </div>

        <h2 class="scoreText" style="margin-top: 20px">Current Turn: {{ currentPlayer === 'playerOne' ? playerOneName : playerTwoName}}</h2>
        <div class="scoreText" style="">{{playerOneName}}: {{ playerOneScore }}</div>
        <div class="scoreText">{{playerTwoName}}: {{ playerTwoScore }}</div>
        <div style="margin-top: 30px">
            <btn class="btn" @click="gameReset">Reset</btn>
            <btn class="btn" @click="settingsOpen = true" style="margin-left: 10px">Settings</btn>
        </div>
        
    </div>

    <!-- <transition> -->
        <div v-if="settingsOpen" id="settings-modal">
            <h1 style="color: pink">Tic Cat Toe Settings</h1>
            <div>
                <label for="playerOneName" style="margin-right: 10px;">Player One Name</label>
                <input name="playerOneName" class="nameInput" v-model="playerOneNameInput"/>
            </div>
            <div style="margin-top: 10px">
                <label for="playerTwoName" style="margin-right: 10px;">Player One Name</label>
                <input name="playerTwoName" class="nameInput" v-model="playerTwoNameInput">
            </div>

            <div style="margin-top: 30px">
                <button class="btn" @click="onSettingsSave">Play</button>
            </div>
        </div>
    <!-- </transition> -->

    <div class="modal" v-if="modal">
        <div style="margin-top: 50px">{{ modalMsg }}</div>

        <div style="margin-top: 50px">
            <button class="btn" style="background: palevioletred" @click="playAgain">Play Again?</button>
        </div>
    </div>
</template>
<script setup>
import {ref} from "vue";
import _ from "lodash-core";

const images = {
    playerOne: "https://t3.ftcdn.net/jpg/05/78/01/02/360_F_578010240_tGolzJLeqVstZhCkteEyuHch5Oj6Fyyh.jpg",
    playerTwo: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRkg7Rbxs_4PaXnjym3u0B1d86DfZvJXKUBXg&s"
};

const playerOneName = ref("Player One");
const playerTwoName = ref("Player Two");

const playerOneNameInput = ref(playerOneName.value);
const playerTwoNameInput = ref(playerTwoName.value);
const onSettingsSave = () => {
    playerOneName.value = playerOneNameInput.value;
    playerTwoName.value = playerTwoNameInput.value;
    settingsOpen.value = false;
}

const settingsOpen = ref(true);
const game = ref({});

const modal = ref(false);
const modalMsg = ref("");

const playAgain = () => {
    reset();
    modal.value = false;
}

// const playerOne = "playerOne";
// const playerTwo= "playerTwo";
const columnsSelected = ref([]);

const currentPlayer = ref("playerOne");

const reset = () => {
    columnsSelected.value = [];
    currentPlayer.value = 'playerTwo';
    game.value = {};
}

const gameReset = () => {
    reset();
    playerOneScore.value = 0;
    playerTwoScore.value = 0;
}


const onClick = (selected) => {
    
    if (settingsOpen.value || modal.value) return;

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
        modalMsg.value = `${playerOneName.value} wins`
        modal.value = true;
        
        playerOneScore.value++;
    }
    const playerTwoWin = checkPlayer(selectedColsByPlayer.playerTwo?.map(c =>  c.col) || []);
    if (playerTwoWin) {
        modalMsg.value = `${playerTwoName.value} wins`
        modal.value = true;

        playerTwoScore.value++;
    }

    if (!playerOneWin && !playerTwoWin && Object.values(val).length === 9)
    {
        modalMsg.value = `It's a draw!`
        modal.value = true;

        playerOneScore.value++;
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
    /* border: 2px solid pink; */
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

#settings-modal {
    z-index: 1;
    width: 100%;
    height: 100vh;
    position: absolute;
    background-color: white;
    top: 0 
}

.nameInput {
    width: 200px;
    height: 30px;
    font-size: 1.25rem;
}

.modal {
    z-index: 2;
    position: absolute; 
    top: 45%;
    left: 0; 
    right: 0; 
    margin-left: auto; 
    margin-right: auto; 
    width: 400px; /* Need a specific value to work */
    height: 200px;
    background-color: pink;
    border-radius: 5px;
    
}

.br {
    border-right: 2px solid pink !important;
    /* border-radius: 5%; */
}

.bt {
    border-top: 2px solid pink;
    /* border-radius: 5%; */
}

.bb {
    border-bottom: 2px solid pink;
    /* border-radius: 5%; */
}

.bl {
    border-left: right 2px solid pink;
    /* border-radius: 5%; */
}

</style>