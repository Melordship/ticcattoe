<template>
  <main class="game-shell">
    <section class="game-panel" aria-label="Tic Cat Toe game">
      <header class="game-header">
        <div>
          <p class="eyebrow">LOCAL MATCH</p>
          <h1>Tic Cat Toe</h1>
        </div>
        <button class="icon-button" type="button" aria-label="Open settings" title="Settings" @click="settingsOpen = true">
          <Settings :size="20" />
        </button>
      </header>

      <div class="scoreboard">
        <div class="player-card" :class="{ active: !result && currentPlayer === 'playerOne' }">
          <span class="player-mark cat-mark">C</span>
          <div><span class="player-label">CAT</span><strong>{{ playerOneName }}</strong></div>
          <b>{{ playerOneScore }}</b>
        </div>
        <div class="versus">VS</div>
        <div class="player-card player-two" :class="{ active: !result && currentPlayer === 'playerTwo' }">
          <span class="player-mark fish-mark">F</span>
          <div><span class="player-label">FISH</span><strong>{{ playerTwoName }}</strong></div>
          <b>{{ playerTwoScore }}</b>
        </div>
      </div>

      <div class="turn-status">
        <span class="status-dot"></span>
        {{ result ? result : `${currentPlayerName}'s turn` }}
      </div>

      <div class="board" role="grid" aria-label="Game board">
        <button
          v-for="(cell, index) in board"
          :key="index"
          class="cell"
          :class="{ occupied: cell, winning: winningCells.includes(index) }"
          type="button"
          role="gridcell"
          :aria-label="cell ? `${cellName(index)}: ${cell === 'playerOne' ? playerOneName : playerTwoName}` : `Place in square ${index + 1}`"
          :disabled="Boolean(cell) || Boolean(result)"
          @click="makeMove(index)"
        >
          <span v-if="cell === 'playerOne'" class="board-mark cat-mark">C</span>
          <span v-if="cell === 'playerTwo'" class="board-mark fish-mark">F</span>
        </button>
      </div>

      <div class="game-actions">
        <button class="secondary-button" type="button" @click="gameReset">
          <RotateCcw :size="17" /> Reset match
        </button>
        <button class="primary-button" type="button" :disabled="!result" @click="playAgain">
          Next round <ArrowRight :size="17" />
        </button>
      </div>
    </section>

    <div v-if="settingsOpen" class="overlay" role="presentation" @click.self="closeSettings">
      <section class="dialog" role="dialog" aria-modal="true" aria-labelledby="settings-title">
        <button class="icon-button dialog-close" type="button" aria-label="Close settings" @click="closeSettings">
          <X :size="20" />
        </button>
        <p class="eyebrow">MATCH SETUP</p>
        <h2 id="settings-title">Choose your names</h2>
        <label>
          <span>Cat player</span>
          <input v-model="playerOneNameInput" maxlength="18" />
        </label>
        <label>
          <span>Fish player</span>
          <input v-model="playerTwoNameInput" maxlength="18" />
        </label>
        <button class="primary-button full-button" type="button" @click="saveSettings">Save & play</button>
      </section>
    </div>
  </main>
</template>
<script setup>
import { computed, ref } from 'vue'
import { ArrowRight, RotateCcw, Settings, X } from '@lucide/vue'

const playerOneName = ref('Mochi')
const playerTwoName = ref('Toast')
const playerOneNameInput = ref(playerOneName.value)
const playerTwoNameInput = ref(playerTwoName.value)
const playerOneScore = ref(0)
const playerTwoScore = ref(0)
const settingsOpen = ref(true)
const currentPlayer = ref('playerOne')
const board = ref(Array(9).fill(null))
const result = ref('')
const winningCells = ref([])

const winningPatterns = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8],
  [0, 3, 6], [1, 4, 7], [2, 5, 8],
  [0, 4, 8], [2, 4, 6]
]

const currentPlayerName = computed(() =>
  currentPlayer.value === 'playerOne' ? playerOneName.value : playerTwoName.value
)

const cellName = (index) => `${Math.floor(index / 3) + 1}, ${index % 3 + 1}`

const saveSettings = () => {
  playerOneName.value = playerOneNameInput.value.trim() || 'Mochi'
  playerTwoName.value = playerTwoNameInput.value.trim() || 'Toast'
  settingsOpen.value = false
}

const closeSettings = () => {
  playerOneNameInput.value = playerOneName.value
  playerTwoNameInput.value = playerTwoName.value
  settingsOpen.value = false
}

const resetBoard = () => {
  board.value = Array(9).fill(null)
  result.value = ''
  winningCells.value = []
}

const playAgain = () => {
  if (!result.value) return
  resetBoard()
}

const gameReset = () => {
  resetBoard()
  playerOneScore.value = 0
  playerTwoScore.value = 0
  currentPlayer.value = 'playerOne'
}

const makeMove = (index) => {
  if (settingsOpen.value || board.value[index] || result.value) return

  const player = currentPlayer.value
  board.value[index] = player

  const winningPattern = winningPatterns.find(pattern =>
    pattern.every(cell => board.value[cell] === player)
  )

  if (winningPattern) {
    winningCells.value = winningPattern
    result.value = `${currentPlayerName.value} wins!`
    if (player === 'playerOne') playerOneScore.value++
    else playerTwoScore.value++
    return
  }

  if (board.value.every(Boolean)) {
    result.value = 'Round draw'
    return
  }

  currentPlayer.value = player === 'playerOne' ? 'playerTwo' : 'playerOne'
}
</script>

<style scoped>
.game-shell {
  min-height: 100vh;
  display: grid;
  place-items: center;
  padding: 24px;
  position: relative;
  overflow: hidden;
}

.game-shell::before {
  content: '';
  position: absolute;
  width: 360px;
  height: 360px;
  left: -140px;
  top: -170px;
  border: 1px solid rgba(35, 70, 67, 0.12);
  border-radius: 50%;
  box-shadow: 0 0 0 70px rgba(35, 70, 67, 0.025), 0 0 0 140px rgba(35, 70, 67, 0.018);
}

.game-panel {
  width: min(520px, 100%);
  position: relative;
  z-index: 1;
}

.game-header, .scoreboard, .game-actions {
  display: flex;
  align-items: center;
}

.game-header {
  justify-content: space-between;
  margin-bottom: 22px;
}

.eyebrow {
  margin: 0 0 4px;
  color: #ed7045;
  font-size: 0.68rem;
  font-weight: 800;
  letter-spacing: 0.18em;
}

h1, h2 { margin: 0; color: #173c38; }
h1 { font-family: Georgia, 'Times New Roman', serif; font-size: clamp(2rem, 8vw, 3rem); line-height: 0.95; }
h2 { font-family: Georgia, 'Times New Roman', serif; font-size: 1.7rem; }

.icon-button {
  width: 42px;
  height: 42px;
  display: grid;
  place-items: center;
  border: 1px solid #d9ded8;
  border-radius: 50%;
  background: rgba(255,255,255,0.72);
  color: #173c38;
  cursor: pointer;
}

.scoreboard {
  gap: 10px;
}

.player-card {
  flex: 1;
  min-width: 0;
  display: grid;
  grid-template-columns: 38px minmax(0, 1fr) auto;
  align-items: center;
  gap: 9px;
  padding: 12px;
  border: 1px solid #e1e5df;
  border-radius: 8px;
  background: rgba(255,255,255,0.78);
  transition: border-color 180ms ease, transform 180ms ease;
}

.player-card.active {
  border-color: #ed7045;
  transform: translateY(-2px);
}

.player-card strong { display: block; overflow: hidden; color: #173c38; text-overflow: ellipsis; }
.player-card > b { color: #173c38; font-size: 1.35rem; }
.player-label { display: block; color: #78857f; font-size: 0.58rem; font-weight: 800; letter-spacing: 0.12em; }
.versus { color: #9ba49e; font-size: 0.65rem; font-weight: 800; }

.player-mark {
  width: 38px;
  height: 38px;
  display: grid;
  place-items: center;
  border-radius: 50%;
  font-family: Georgia, serif;
  font-weight: 800;
}
.cat-mark { background: #ed7045; color: white; }
.fish-mark { background: #2d9c8e; color: white; }

.turn-status {
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: #65746f;
  font-size: 0.82rem;
  font-weight: 700;
}
.status-dot { width: 6px; height: 6px; border-radius: 50%; background: #ed7045; }

.board {
  aspect-ratio: 1;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  padding: 10px;
  border-radius: 8px;
  background: #173c38;
  box-shadow: 0 24px 70px rgba(23, 60, 56, 0.18);
}

.cell {
  aspect-ratio: 1;
  display: grid;
  place-items: center;
  border: 0;
  border-radius: 6px;
  background: #f8f7f0;
  cursor: pointer;
  transition: transform 160ms ease, background 160ms ease;
}
.cell:not(:disabled):hover { background: white; transform: scale(0.97); }
.cell.occupied { cursor: default; }
.cell.winning { background: #ffe4d7; }
.board-mark {
  width: 58%;
  height: 58%;
  display: grid;
  place-items: center;
  border-radius: 50%;
  font-family: Georgia, serif;
  font-size: clamp(1.2rem, 7vw, 2.2rem);
  font-weight: 800;
  animation: mark-in 220ms cubic-bezier(.2,.9,.2,1.25);
}

.game-actions { justify-content: space-between; gap: 12px; margin-top: 18px; }
.primary-button, .secondary-button {
  min-height: 44px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  border-radius: 6px;
  padding: 0 18px;
  font-weight: 800;
  cursor: pointer;
}
.primary-button { border: 0; background: #ed7045; color: white; }
.secondary-button { border: 1px solid #d6dcd6; background: transparent; color: #173c38; }
button:disabled { opacity: 0.45; cursor: not-allowed; }

.overlay {
  position: fixed;
  z-index: 10;
  inset: 0;
  display: grid;
  place-items: center;
  padding: 20px;
  background: rgba(16, 43, 40, 0.68);
  backdrop-filter: blur(10px);
}
.dialog {
  width: min(410px, 100%);
  position: relative;
  display: grid;
  gap: 16px;
  padding: 34px;
  border-radius: 8px;
  background: #fdfcf7;
  box-shadow: 0 30px 90px rgba(0,0,0,0.28);
}
.dialog-close { position: absolute; top: 16px; right: 16px; }
.dialog label { display: grid; gap: 7px; color: #52625d; font-size: 0.78rem; font-weight: 800; }
.dialog input { height: 46px; border: 1px solid #d8ddd7; border-radius: 6px; padding: 0 13px; background: white; color: #173c38; font-size: 1rem; }
.dialog input:focus { outline: 2px solid rgba(237,112,69,0.3); border-color: #ed7045; }
.full-button { width: 100%; }

@keyframes mark-in { from { opacity: 0; transform: scale(0.4) rotate(-12deg); } to { opacity: 1; transform: scale(1) rotate(0); } }
@media (max-width: 520px) {
  .game-shell { align-items: start; padding: 18px 14px; }
  .game-panel { padding-top: 8px; }
  .player-card { grid-template-columns: 34px minmax(0, 1fr) auto; padding: 9px; }
  .player-mark { width: 34px; height: 34px; }
  .game-actions { display: grid; grid-template-columns: 1fr 1fr; }
  .primary-button, .secondary-button { padding: 0 10px; }
  .dialog { padding: 30px 22px 22px; }
}
@media (prefers-reduced-motion: reduce) { *, *::before, *::after { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; } }
</style>