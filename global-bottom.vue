<template>
  <div class="global-bottom">
    <div class="global-bottom-line"></div>
  </div>
</template>

<style scoped>
.global-bottom {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 100;
  pointer-events: none;
}

.global-bottom-line {
  height: 2px;
  background: linear-gradient(
    90deg,
    rgba(148, 163, 184, 0) 0%,
    rgba(148, 163, 184, 0.15) 15%,
    rgba(148, 163, 184, 0.15) 85%,
    rgba(148, 163, 184, 0) 100%
  );
}
</style>
