<script setup lang="ts">
defineProps<{
  image: string;
  status: 'closed' | 'opened' | 'matched';
  disabled?: boolean;
}>();
</script>
<template>
  <div
    class="card"
    :class="{
      flipped: status === 'opened' || status === 'matched',
      matched: status === 'matched',
      disabled,
    }"
  >
    <div class="card-inner">
      <div class="card-face card-back"></div>
      <div class="card-face card-front">
        <img :src="image" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  height: 120px;
  perspective: 1000px;
  cursor: pointer;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.6s;
}

.card.flipped .card-inner {
  transform: rotateY(180deg);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 8px;
  box-shadow: none;
}

.card-back {
  background-color: #daa9ef;
  border: 2px solid #c044d9;
}

.card-front {
  background-color: #fff;
  border: 2px solid #e0e0e0;
  transform: rotateY(180deg);
}

.card-front img {
  width: 60%;
  height: 60%;
  object-fit: contain;
}

.card.matched .card-front {
  background-color: #e6ffe6;
  border-color: #34c759;
}

.card.disabled {
  pointer-events: none;
}

@media (max-width: 768px) {
  .card {
    height: 100px;
    min-width: 150px;
  }
  .card-front img {
    width: 50%;
    height: 50%;
  }
}

@media (max-width: 480px) {
  .card {
    height: 80px;
    min-width: 100px;
  }
  .card-front img {
    width: 40%;
    height: 40%;
  }
}
</style>
