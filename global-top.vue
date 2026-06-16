<template>
  <div class="global-top">
    <Transition name="top-text" mode="out-in">
      <span class="global-top-title" :key="isCover ? 'chapter' : 'topic'">
        {{ isCover ? "第十章 静电场" : "电容器的电容" }}
      </span>
    </Transition>
    <img src="/images/logo.png" alt="东北育才学校" class="global-top-logo" />
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";

const nav = ($slidev as any).nav;
const isCover = computed(() => nav.currentPage === 1);
</script>

<style scoped>
.global-top {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 2.5rem;
  z-index: 100;
  pointer-events: none;
}

.global-top-title {
  font-size: 0.65rem;
  font-weight: 600;
  color: rgba(241, 245, 249, 0.45);
  letter-spacing: 0.06em;
  white-space: nowrap;
  line-height: 1;
}

.global-top-logo {
  height: 1.4rem;
  width: auto;
  opacity: 0.7;
}

/* 文字切换动画 */
.top-text-enter-active,
.top-text-leave-active {
  transition: all 0.45s cubic-bezier(0.4, 0, 0.2, 1);
}

.top-text-enter-from {
  opacity: 0;
  transform: translateY(-12px) scale(0.95);
}

.top-text-leave-to {
  opacity: 0;
  transform: translateY(12px) scale(1.05);
}
</style>
