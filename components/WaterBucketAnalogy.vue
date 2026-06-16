<script setup lang="ts">
import { ref, computed, onUnmounted } from "vue";

const level = ref(0);
const animating = ref(false);
const dragging = ref(false);

// 极板颜色：灰→红/蓝 渐变（与其他组件一致）
function interpolateColor(c1: string, c2: string, t: number) {
  const p = (h: string) => ({ r: parseInt(h.slice(0, 2), 16), g: parseInt(h.slice(2, 4), 16), b: parseInt(h.slice(4, 6), 16) });
  const a = p(c1), b = p(c2);
  const r = Math.round(a.r + t * (b.r - a.r));
  const g = Math.round(a.g + t * (b.g - a.g));
  const bl = Math.round(a.b + t * (b.b - a.b));
  return `rgb(${r},${g},${bl})`;
}
const upperColor = computed(() => interpolateColor("475569", "ef4444", level.value));
const lowerColor = computed(() => interpolateColor("475569", "3b82f6", level.value));

// 按电压递增依次出现 1→5 个电荷，始终居中
const chargeXs = computed(() => {
  const count = level.value <= 0.02 ? 0 : Math.min(5, Math.max(1, Math.ceil(level.value * 5)));
  const sets = [
    [475],
    [460, 490],
    [448, 475, 502],
    [436, 462, 488, 514],
    [430, 452, 475, 498, 520],
  ];
  return count === 0 ? [] : sets[count - 1];
});
let timer: any = null;
let dragStartY = 0;
let dragStartLevel = 0;
const svgRef = ref<SVGElement | null>(null);

function svgY(clientY: number): number {
  if (!svgRef.value) return 223;
  const rect = svgRef.value.getBoundingClientRect();
  const scale = 230 / rect.height;
  return (clientY - rect.top) * scale;
}

function yToLevel(y: number): number {
  return Math.max(0, Math.min(1, (223 - y) / 131));
}

function toggle() {
  if (animating.value) return;
  if (level.value > 0.9) {
    level.value = 0;
    return;
  }
  animating.value = true;
  level.value = 0;
  const steps = 60;
  let step = 0;
  timer = setInterval(() => {
    step++;
    const t = step / steps;
    level.value = Math.min(1, 1 - Math.exp(-5 * t));
    if (step >= steps) {
      clearInterval(timer);
      timer = null;
      level.value = 1;
      animating.value = false;
    }
  }, 30);
}

function onPointerDown(e: PointerEvent) {
  if (animating.value) {
    clearInterval(timer);
    timer = null;
    animating.value = false;
  }
  dragging.value = true;
  dragStartY = e.clientY;
  dragStartLevel = level.value;
  level.value = yToLevel(svgY(e.clientY));
  if (svgRef.value) svgRef.value.setPointerCapture(e.pointerId);
}

function onPointerMove(e: PointerEvent) {
  if (!dragging.value) return;
  level.value = yToLevel(svgY(e.clientY));
}

function onPointerUp(e: PointerEvent) {
  dragging.value = false;
  if (Math.abs(e.clientY - dragStartY) < 4) {
    level.value = dragStartLevel;
    toggle();
  }
}

onUnmounted(() => {
  if (timer) clearInterval(timer);
});
</script>

<template>
  <div
    class="bucket-root"
    :class="{ 'is-dragging': dragging }"
    @pointerdown="onPointerDown"
    @pointermove="onPointerMove"
    @pointerup="onPointerUp"
  >
    <svg ref="svgRef" viewBox="0 0 680 230" class="bucket-svg">
      <!-- ═══════ 左侧：水桶 ═══════ -->
      <text x="210" y="18" text-anchor="middle" font-size="14" font-weight="800" fill="#94a3b8">
        水桶装水
      </text>

      <path
        d="M 105 55 L 105 215 Q 105 225 115 225 L 305 225 Q 315 225 315 215 L 315 55"
        fill="none"
        stroke="#64748b"
        stroke-width="3"
      />

      <defs>
        <clipPath id="bC"><rect x="107" y="57" width="206" height="166" rx="2" /></clipPath>
      </defs>
      <rect
        x="107"
        :y="223 - level * 131"
        width="206"
        :height="level * 131 + 2"
        fill="rgba(59,130,246,0.35)"
        clip-path="url(#bC)"
      />

      <line
        v-if="level > 0.02"
        x1="108"
        :y1="223 - level * 131"
        x2="314"
        :y2="223 - level * 131"
        stroke="rgba(147,197,253,0.5)"
        stroke-width="1.5"
      />

      <!-- ═══ 水龙头 🚰 ═══ -->
      <!-- 水平管身 -->
      <rect x="60" y="74" width="53" height="13" rx="4" fill="#2d3c54" />
      <rect x="61" y="75" width="51" height="11" rx="3.5" fill="#3b4f6b" />
      <rect x="62" y="76" width="49" height="3.5" rx="1.5" fill="rgba(255,255,255,0.1)" />

      <!-- 阀门（紧凑） -->
      <rect x="76" y="70" width="11" height="5" rx="1.5" fill="#334155" />
      <rect x="80" y="62" width="3" height="9" rx="1" fill="#475569" />
      <rect x="72" y="59" width="19" height="4" rx="1.5" fill="#e2a846" stroke="#b8860b" stroke-width="0.6" />
      <circle cx="81.5" cy="61" r="3" fill="#f59e0b" stroke="#b8860b" stroke-width="0.6" />

      <!-- 出水弯管：无外层边框，与管身无缝衔接 -->
      <!-- 弯管从管身内部 x=103 出发，中心线 y=80 -->
      <path d="M 103 80 L 114 80 Q 120 80 120 86 L 120 91" fill="none" stroke="#3b4f6b" stroke-width="11" stroke-linecap="round" />
      <path d="M 104 80 L 113 80 Q 118 80 118 85 L 118 89" fill="none" stroke="rgba(255,255,255,0.08)" stroke-width="3" stroke-linecap="round" />

      <!-- 出水嘴 -->
      <ellipse cx="120" cy="93" rx="5.5" ry="2.5" fill="#1e293b" stroke="#334155" stroke-width="0.8" />
      <ellipse cx="120" cy="93" rx="3.5" ry="1.3" fill="#0f172a" />

      <!-- 水面参考虚线 -->
      <line x1="102" y1="92" x2="315" y2="92" stroke="#64748b" stroke-width="0.6" stroke-dasharray="3,5" opacity="0.3" />

      <!-- 水滴 -->
      <g v-if="animating && level < 0.9">
        <circle cx="120" cy="98" r="2.8" fill="#60a5fa" opacity="0.55">
          <animate attributeName="cy" from="95" to="220" dur="0.9s" repeatCount="indefinite" />
          <animate attributeName="opacity" from="0.55" to="0" dur="0.9s" repeatCount="indefinite" />
          <animate attributeName="r" from="2.8" to="1.3" dur="0.9s" repeatCount="indefinite" />
        </circle>
        <circle cx="120" cy="103" r="1.8" fill="#60a5fa" opacity="0.25">
          <animate attributeName="cy" from="101" to="220" dur="0.9s" begin="0.22s" repeatCount="indefinite" />
          <animate attributeName="opacity" from="0.25" to="0" dur="0.9s" begin="0.22s" repeatCount="indefinite" />
        </circle>
      </g>

      <!-- h：桶底 → 水面 -->
      <line
        x1="330"
        y1="223"
        x2="345"
        y2="223"
        stroke="#64748b"
        stroke-width="1"
        stroke-dasharray="3,3"
      />
      <line
        x1="330"
        :y1="223 - level * 131"
        x2="345"
        :y2="223 - level * 131"
        stroke="#64748b"
        stroke-width="1"
        stroke-dasharray="3,3"
      />
      <line x1="338" y1="223" x2="338" :y2="223 - level * 131" stroke="#e2a846" stroke-width="2" />
      <!-- h 顶端箭头（指向上） -->
      <polygon
        :points="`334,${223 - level * 131 + 8} 338,${223 - level * 131 - 1} 342,${223 - level * 131 + 8}`"
        fill="#e2a846"
      />
      <!-- h 底端箭头（指向下） -->
      <polygon :points="`334,${223 - 8} 338,${223 + 1} 342,${223 - 8}`" fill="#e2a846" />
      <text x="353" :y="223 - level * 65" font-size="14" font-weight="800" fill="#e2a846">h</text>

      <!-- ═══════ 右侧：电容器 ═══════ -->
      <text x="480" y="18" text-anchor="middle" font-size="14" font-weight="800" fill="#94a3b8">
        电容器充电
      </text>

      <line x1="480" y1="32" x2="480" y2="98" stroke="#64748b" stroke-width="2" />
      <line x1="480" y1="155" x2="480" y2="220" stroke="#64748b" stroke-width="2" />

      <line
        x1="415"
        y1="98"
        x2="535"
        y2="98"
        :stroke="upperColor"
        stroke-width="5"
        stroke-linecap="round"
      />
      <line
        x1="415"
        y1="155"
        x2="535"
        y2="155"
        :stroke="lowerColor"
        stroke-width="5"
        stroke-linecap="round"
      />

      <text v-for="x in chargeXs" :key="'+'+x" :x="x" y="108" style="font-size:14px" font-weight="950" fill="#f87171" text-anchor="middle" dominant-baseline="central">+</text>
      <text v-for="x in chargeXs" :key="'-'+x" :x="x" y="145" style="font-size:14px" font-weight="950" fill="#60a5fa" text-anchor="middle" dominant-baseline="central">−</text>

      <!-- U：从两极板中间向上下对称展开 -->
      <line
        x1="550"
        y1="98"
        x2="580"
        y2="98"
        stroke="#64748b"
        stroke-width="1"
        stroke-dasharray="3,3"
      />
      <line
        x1="550"
        y1="155"
        x2="580"
        y2="155"
        stroke="#64748b"
        stroke-width="1"
        stroke-dasharray="3,3"
      />
      <line
        x1="565"
        :y1="126.5 - level * 28.5"
        :x2="565"
        :y2="126.5 + level * 28.5"
        stroke="#f59e0b"
        stroke-width="2.5"
      />
      <!-- U 上端箭头（指向上） -->
      <polygon
        :points="`561,${126.5 - level * 28.5 + 7} 565,${126.5 - level * 28.5 - 1} 569,${126.5 - level * 28.5 + 7}`"
        fill="#f59e0b"
      />
      <!-- U 下端箭头（指向下） -->
      <polygon
        :points="`561,${126.5 + level * 28.5 - 7} 565,${126.5 + level * 28.5 + 1} 569,${126.5 + level * 28.5 - 7}`"
        fill="#f59e0b"
      />
      <text x="590" y="130" font-size="15" font-weight="800" fill="#f59e0b">U</text>
    </svg>

    <!-- 极性图例 -->
    <div class="flex items-center justify-center gap-6 mt-2">
      <span class="inline-flex items-center gap-2 text-sm font-medium opacity-80"
        ><span class="inline-block w-3.5 h-3.5 rounded-sm" style="background: #f87171"></span>
        正电（红）</span>
      <span class="inline-flex items-center gap-2 text-sm font-medium opacity-80"
        ><span class="inline-block w-3.5 h-3.5 rounded-sm" style="background: #60a5fa"></span>
        负电（蓝）</span>
    </div>
  </div>
</template>

<style scoped>
.bucket-root {
  width: 100%;
  cursor: ns-resize;
  user-select: none;
  touch-action: none;
}
.bucket-root.is-dragging {
  cursor: grabbing;
}
.bucket-svg {
  display: block;
  width: 100%;
  height: auto;
}
.bucket-svg text {
  font-family:
    system-ui,
    -apple-system,
    BlinkMacSystemFont,
    "Segoe UI",
    Roboto,
    sans-serif !important;
  user-select: none !important;
  pointer-events: none !important;
}
</style>
