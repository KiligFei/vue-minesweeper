<script lang="ts" setup>
import { GamePlay } from '~/composables/logic'
const play = new GamePlay(10, 10, 20)
useStorage('v-state', play.state)
const state = $computed(() => play.board)

const mineRest = $computed(() => {
  if (!play.state.value.mineGenerated)
    return play.mines
  return play.blocks.reduce((a, b) => a + (b.mine ? 1 : 0) - (b.flagged ? 1 : 0), 0)
})

watchEffect(() => {
  play.checkGameState()
})
function newGame(difficulty: 'easy' | 'medium' | 'hard') {
  switch (difficulty) {
    case 'easy':
      play.reset(9, 9, 10)
      break
    case 'medium':
      play.reset(16, 16, 40)
      break
    case 'hard':
      play.reset(16, 30, 99)
      break
  }
}
</script>

<template>
  <div>
    <div i-carbon-campsite text-4xl inline-block />
  </div>
  <div flex="~ gap1" justify-center p4>
    <button btn @click="play.reset()">
      New Game
    </button>
    <button btn @click="newGame('easy')">
      Easy
    </button>
    <button btn @click="newGame('medium')">
      Medium
    </button>
    <button btn @click="newGame('hard')">
      Hard
    </button>
  </div>

  <div flex="~ gap-10" justify-center>
    <div font-mono text-2xl flex="~ gap-1" items-center>
      <div i-mdi-mine />
      {{ mineRest }}
    </div>
  </div>
  <div p5>
    <div v-for="row, y in state" :key="y" flex="~" items-center justify-center>
      <MineBlock v-for="block, x in row" :key="x" :block="block" @click="play.onClick(block)"
        @contextmenu.prevent="play.onRightClick(block)" />
    </div>
  </div>
  <Confetti :passed="play.state.value.gameState === 'won'" />
</template>
