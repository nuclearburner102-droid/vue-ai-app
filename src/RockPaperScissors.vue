<template>
  <div class="rps">
    <h1>Rock Paper Scissors</h1>
    <p class="subtitle">First to 5 wins · Keys: 1 Rock · 2 Paper · 3 Scissors · Space Reset</p>

    <div class="scoreboard">
      <div class="score-item">
        <div class="score-label">You</div>
        <div class="score-value">{{ playerScore }}</div>
      </div>
      <div class="score-item">
        <div class="score-label">Ties</div>
        <div class="score-value">{{ ties }}</div>
      </div>
      <div class="score-item">
        <div class="score-label">Computer</div>
        <div class="score-value">{{ computerScore }}</div>
      </div>
    </div>

    <div class="choices">
      <button
        v-for="choice in choices"
        :key="choice.name"
        class="choice-btn"
        :disabled="isThinking || matchOver"
        @click="play(choice.name)"
      >
        <span class="choice-emoji">{{ choice.emoji }}</span>
        {{ choice.name }}
      </button>
    </div>

    <div class="result-area">
      <template v-if="matchOver">
        <div class="match-winner" :class="matchWinner">
          {{ matchWinner === 'player' ? 'You won the match!' : 'Computer won the match!' }}
        </div>
        <p class="hint">Press Space or click Reset to play again</p>
      </template>
      <template v-else-if="playerChoice">
        <div class="hands">
          <span>{{ emojiFor(playerChoice) }}</span>
          <span class="vs">vs</span>
          <span>{{ emojiFor(computerChoice) }}</span>
        </div>
        <div class="outcome" :class="result">{{ resultText }}</div>
      </template>
      <p v-else class="hint">Choose rock, paper, or scissors to start</p>
    </div>

    <button
      class="reset-btn"
      :disabled="playerScore === 0 && computerScore === 0 && ties === 0 && !matchOver"
      @click="resetScore"
    >
      Reset Score
    </button>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const choices = [
  { name: 'Rock', emoji: '🪨' },
  { name: 'Paper', emoji: '📄' },
  { name: 'Scissors', emoji: '✂️' },
]

const WIN_SCORE = 5

const playerScore = ref(0)
const computerScore = ref(0)
const ties = ref(0)

const playerChoice = ref(null)
const computerChoice = ref(null)
const result = ref(null) // 'win' | 'lose' | 'tie'
const isThinking = ref(false)
const matchOver = ref(false)
const matchWinner = ref(null) // 'player' | 'computer'

const emojiFor = (name) => {
  const found = choices.find((c) => c.name === name)
  return found ? found.emoji : ''
}

const resultText = computed(() => {
  if (result.value === 'win') return 'You win!'
  if (result.value === 'lose') return 'Computer wins!'
  if (result.value === 'tie') return "It's a tie!"
  return ''
})

const getWinner = (player, computer) => {
  if (player === computer) return 'tie'
  if (
    (player === 'Rock' && computer === 'Scissors') ||
    (player === 'Paper' && computer === 'Rock') ||
    (player === 'Scissors' && computer === 'Paper')
  ) {
    return 'win'
  }
  return 'lose'
}

const checkMatchWinner = () => {
  if (playerScore.value >= WIN_SCORE) {
    matchOver.value = true
    matchWinner.value = 'player'
  } else if (computerScore.value >= WIN_SCORE) {
    matchOver.value = true
    matchWinner.value = 'computer'
  }
}

const play = (choice) => {
  if (isThinking.value || matchOver.value) return

  isThinking.value = true
  playerChoice.value = choice
  computerChoice.value = null
  result.value = null

  setTimeout(() => {
    const randomIndex = Math.floor(Math.random() * choices.length)
    computerChoice.value = choices[randomIndex].name

    const outcome = getWinner(choice, computerChoice.value)
    result.value = outcome

    if (outcome === 'win') playerScore.value++
    else if (outcome === 'lose') computerScore.value++
    else ties.value++

    checkMatchWinner()
    isThinking.value = false
  }, 400)
}

const resetScore = () => {
  playerScore.value = 0
  computerScore.value = 0
  ties.value = 0
  playerChoice.value = null
  computerChoice.value = null
  result.value = null
  matchOver.value = false
  matchWinner.value = null
}

const handleKeydown = (e) => {
  // Ignore if user is typing in an input/textarea
  if (e.target.tagName === 'INPUT' || e.target.tagName === 'TEXTAREA') return

  if (e.code === 'Space') {
    e.preventDefault()
    resetScore()
    return
  }

  if (matchOver.value || isThinking.value) return

  if (e.key === '1') play('Rock')
  else if (e.key === '2') play('Paper')
  else if (e.key === '3') play('Scissors')
}

onMounted(() => {
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
})
</script>

<style scoped>
.rps {
  max-width: 480px;
  margin: 0 auto;
  padding: 2rem 1.5rem;
  font-family: system-ui, -apple-system, sans-serif;
  color: #f1f5f9;
  background: #1e293b;
  border-radius: 1.25rem;
}

h1 {
  text-align: center;
  font-size: 1.75rem;
  margin-bottom: 0.25rem;
}

.subtitle {
  text-align: center;
  color: #94a3b8;
  font-size: 0.85rem;
  margin-bottom: 1.75rem;
  line-height: 1.4;
}

.scoreboard {
  display: flex;
  justify-content: space-between;
  background: rgba(0, 0, 0, 0.25);
  border-radius: 0.75rem;
  padding: 0.85rem 1.25rem;
  margin-bottom: 1.75rem;
}

.score-item {
  text-align: center;
}

.score-label {
  font-size: 0.75rem;
  color: #94a3b8;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.2rem;
}

.score-value {
  font-size: 1.5rem;
  font-weight: 700;
}

.choices {
  display: flex;
  gap: 0.75rem;
  margin-bottom: 1.75rem;
}

.choice-btn {
  flex: 1;
  background: rgba(255, 255, 255, 0.05);
  border: 2px solid rgba(255, 255, 255, 0.1);
  border-radius: 1rem;
  padding: 1.1rem 0.5rem;
  cursor: pointer;
  color: #f1f5f9;
  font-size: 0.95rem;
  font-weight: 600;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.4rem;
  transition: all 0.15s ease;
}

.choice-btn:hover:not(:disabled) {
  background: rgba(56, 189, 248, 0.15);
  border-color: #38bdf8;
  transform: translateY(-2px);
}

.choice-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.choice-emoji {
  font-size: 2rem;
  line-height: 1;
}

.result-area {
  min-height: 7.5rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.75rem;
  margin-bottom: 1.5rem;
}

.hands {
  display: flex;
  align-items: center;
  gap: 1.5rem;
  font-size: 2.75rem;
}

.vs {
  font-size: 0.9rem;
  color: #94a3b8;
  font-weight: 600;
}

.outcome {
  font-size: 1.35rem;
  font-weight: 700;
}

.outcome.win {
  color: #22c55e;
}

.outcome.lose {
  color: #ef4444;
}

.outcome.tie {
  color: #eab308;
}

.match-winner {
  font-size: 1.5rem;
  font-weight: 700;
  text-align: center;
}

.match-winner.player {
  color: #22c55e;
}

.match-winner.computer {
  color: #ef4444;
}

.hint {
  color: #94a3b8;
  font-size: 0.85rem;
}

.reset-btn {
  width: 100%;
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.15);
  color: #94a3b8;
  border-radius: 0.75rem;
  padding: 0.7rem;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.15s ease;
}

.reset-btn:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.05);
  color: #f1f5f9;
}

.reset-btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}
</style>