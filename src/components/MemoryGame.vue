<script setup lang="ts">
import { ref, computed, watch } from 'vue';
import MemoryCard from './MemoryCard.vue';
import { images } from '../images.ts';
import { shuffleCards } from './shuffleCards.ts';
import type { Card } from '@/types.ts';

const CLOSE_TIMEOUT = 1000;
const cards = ref<Card[]>([]);
const moves = ref(0);
const openedCards = ref(new Set<number>());
const matchedCards = ref(new Set<number>());
const matches = computed(() => matchedCards.value.size / 2);
const isGameWon = computed(() => matches.value === cards.value.length / 2);
const isTwoCardsOpened = computed(() => openedCards.value.size === 2);

const openCard = (index: number) => {
  openedCards.value.add(index);
};

const getStatus = (index: number) => {
  if (openedCards.value.has(index)) {
    return 'opened';
  }
  if (matchedCards.value.has(index)) {
    return 'matched';
  }
  return 'closed';
};

watch(isTwoCardsOpened, () => {
  if (!isTwoCardsOpened.value) return;

  setTimeout(() => {
    moves.value += 1;
    const [card1, card2] = Array.from(openedCards.value);
    if (cards.value[card1].name === cards.value[card2].name) {
      matchedCards.value.add(card1);
      matchedCards.value.add(card2);
    }
    openedCards.value.clear();
  }, CLOSE_TIMEOUT);
});

const resetGame = () => {
  moves.value = 0;
  openedCards.value.clear();
  matchedCards.value.clear();
  cards.value = shuffleCards([...images, ...images]);
};

resetGame();
</script>

<template>
  <div class="memory-game">
    <h1 class="memory-game__title">Memory Game</h1>
    <div class="memory-game__info">
      <div>Moves: {{ moves }}</div>
      <div>Matches: {{ matches }} / {{ cards.length / 2 }}</div>
    </div>
    <div class="memory-game__container">
      <MemoryCard
        v-for="(card, index) in cards"
        :key="index"
        :image="card.image"
        :status="getStatus(index)"
        :disabled="isTwoCardsOpened"
        @click="openCard(index)"
      />
    </div>
    <button class="memory-game__btn" @click="resetGame">New game</button>
    <div v-if="isGameWon" class="memory-game__win-message">
      Congratulations! You won in {{ moves }} moves!
    </div>
  </div>
</template>

<style scoped>
.memory-game {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

.memory-game__container {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(4, 120px);
  gap: 10px;
  margin: 0 auto;
  width: 100%;
}

.memory-game__title {
  color: #2c3e50;
  margin-bottom: 20px;
}

.memory-game__info {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
  padding: 0 20px;
  font-size: 1.2rem;
  color: #34495e;
}

.game-info div {
  font-size: 1.2rem;
  font-weight: bold;
  color: #34495e;
}

.memory-game__btn {
  margin-top: 20px;
  padding: 12px 24px;
  background-color: #8e44ad;
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s;
}

.memory-game__btn:hover {
  background-color: #7a288a;
}

.memory-game__win-message {
  margin-top: 20px;
  font-size: 1.5rem;
  color: #27ae60;
  font-weight: bold;
}

@media (max-width: 768px) {
  .memory-game__container {
    grid-template-columns: repeat(3, 1fr);
  }
}
</style>
