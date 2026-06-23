<script setup lang="ts">
import { ref, computed, onUnmounted } from "vue";

// State definition
const qA = ref<number>(0); // Charge on Cap 1 (voltage range 0 to 8 V)
const qB = ref<number>(0); // Charge on Cap 2
const s1Pos = ref<"open" | "1" | "2">("open"); // Switch 1 position
const s2Closed = ref<boolean>(false); // Switch 2 closed/open status

const simState = ref<"idle" | "charging_A" | "sharing" | "discharging_B" | "discharging_A_and_B">(
  "idle",
);
const electronOffset = ref<number>(0);

let timerId: any = null;

// Color helper function
function interpolateColor(color1: string, color2: string, factor: number) {
  const parseHex = (hex: string) => {
    const match = hex.replace("#", "");
    const r = parseInt(match.substring(0, 2), 16);
    const g = parseInt(match.substring(2, 4), 16);
    const b = parseInt(match.substring(4, 6), 16);
    return { r, g, b };
  };
  const c1 = parseHex(color1);
  const c2 = parseHex(color2);
  const r = Math.round(c1.r + factor * (c2.r - c1.r));
  const g = Math.round(c1.g + factor * (c2.g - c1.g));
  const b = Math.round(c1.b + factor * (c2.b - c1.b));
  return `rgb(${r}, ${g}, ${b})`;
}

// Compute colors based on voltages (charge size, where 8.0 is max)
const aUpperColor = computed(() =>
  interpolateColor("#475569", "#ef4444", Math.min(1, qA.value / 8.0)),
);
const aLowerColor = computed(() =>
  interpolateColor("#475569", "#3b82f6", Math.min(1, qA.value / 8.0)),
);
const bUpperColor = computed(() =>
  interpolateColor("#475569", "#ef4444", Math.min(1, qB.value / 8.0)),
);
const bLowerColor = computed(() =>
  interpolateColor("#475569", "#3b82f6", Math.min(1, qB.value / 8.0)),
);

// 电荷符号：随电压比例递增 1→5 个，始终居中
function makeChargeXs(q: () => number, center: number) {
  return computed(() => {
    const t = Math.min(1, q() / 8.0);
    const c = t <= 0.02 ? 0 : Math.min(5, Math.max(1, Math.ceil(t * 5)));
    const sets = [
      [center],
      [center - 8, center + 8],
      [center - 15, center, center + 15],
      [center - 20, center - 8, center + 8, center + 20],
      [center - 20, center - 10, center, center + 10, center + 20],
    ];
    return c === 0 ? [] : sets[c - 1];
  });
}
const chargeXsC1 = makeChargeXs(() => qA.value, 420);
const chargeXsC2 = makeChargeXs(() => qB.value, 150);

// Switch 1 Target - Mathematically exact rotation of length L = 72
const s1Target = computed(() => {
  if (s1Pos.value === "1") return { x: 252.3, y: 45.5 }; // Length = 72 at 160.1 degrees
  if (s1Pos.value === "2") return { x: 252.3, y: 94.5 }; // Length = 72 at 199.9 degrees
  return { x: 248.0, y: 70.0 }; // Length = 72 at 180 degrees
});

// S₂ 枢轴在 (205,99)，稍左于S₁触点2；向右下~28°摆到接线柱3（断开），正下摆到接线柱4（接地）
const s2Target = computed(() => {
  return s2Closed.value
    ? { x: 205, y: 178 } // Closed: 正下连接接线柱4（接地，竖直）
    : { x: 242, y: 169 }; // Open: 右下~28°连接接线柱3（断开）
});

// Simple and literal status texts requested by user
const s1StatusText = computed(() => {
  if (s1Pos.value === "1") return "接通电源";
  if (s1Pos.value === "2") return "连接另一电容器";
  return "断开";
});

const s2StatusText = computed(() => {
  return s2Closed.value ? "接通" : "断开";
});

// Trigger simulation logic when switches are operated
function toggleS1(pos: "open" | "1" | "2") {
  if (timerId) {
    clearInterval(timerId);
    timerId = null;
  }
  s1Pos.value = pos;
  runPhysicsStep();
}

function toggleS2(closed: boolean) {
  if (timerId) {
    clearInterval(timerId);
    timerId = null;
  }
  s2Closed.value = closed;
  runPhysicsStep();
}

function runPhysicsStep() {
  // Case 1: S1 to 1 (Charge A up to battery 8.0 V)
  if (s1Pos.value === "1") {
    simState.value = "charging_A";
    const startA = qA.value;
    const steps = 15;
    let step = 0;

    timerId = setInterval(() => {
      step++;
      electronOffset.value += 5;
      qA.value = startA + (8.0 - startA) * (step / steps);

      if (step >= steps) {
        clearInterval(timerId);
        timerId = null;
        qA.value = 8.0;
        simState.value = "idle";
      }
    }, 25);
  }

  // Case 2: S1 to 2 (Parallel / Sharing)
  else if (s1Pos.value === "2") {
    // Subcase 2a: S2 is closed -> any connected charge immediately shorts to ground!
    if (s2Closed.value) {
      simState.value = qA.value > 0 || qB.value > 0 ? "discharging_A_and_B" : "idle";
      const startA = qA.value;
      const startB = qB.value;
      const steps = 15;
      let step = 0;

      timerId = setInterval(() => {
        step++;
        electronOffset.value += 6;
        qA.value = startA * (1 - step / steps);
        qB.value = startB * (1 - step / steps);

        if (step >= steps) {
          clearInterval(timerId);
          timerId = null;
          qA.value = 0;
          qB.value = 0;
          simState.value = "idle";
        }
      }, 25);
    }
    // Subcase 2b: S2 is open -> dynamic charge sharing between A and B!
    else {
      const avgExchange = (qA.value + qB.value) / 2;
      const diff = Math.abs(qA.value - qB.value);
      if (diff < 0.02) {
        simState.value = "idle";
        return;
      }

      simState.value = "sharing";
      const startA = qA.value;
      const startB = qB.value;
      const steps = 15;
      let step = 0;

      timerId = setInterval(() => {
        step++;
        electronOffset.value += 5;
        qA.value = startA + (avgExchange - startA) * (step / steps);
        qB.value = startB + (avgExchange - startB) * (step / steps);

        if (step >= steps) {
          clearInterval(timerId);
          timerId = null;
          qA.value = avgExchange;
          qB.value = avgExchange;
          simState.value = "idle";
        }
      }, 25);
    }
  }

  // Case 3: S1 is open (separated)
  else if (s1Pos.value === "open") {
    // If S2 is closed, B discharges immediately
    if (s2Closed.value && qB.value > 0) {
      simState.value = "discharging_B";
      const startB = qB.value;
      const steps = 10;
      let step = 0;

      timerId = setInterval(() => {
        step++;
        electronOffset.value += 8;
        qB.value = startB * (1 - step / steps);

        if (step >= steps) {
          clearInterval(timerId);
          timerId = null;
          qB.value = 0;
          simState.value = "idle";
        }
      }, 25);
    } else {
      simState.value = "idle";
    }
  }
}

function performReset() {
  if (timerId) {
    clearInterval(timerId);
    timerId = null;
  }
  qA.value = 0;
  qB.value = 0;
  s1Pos.value = "open";
  s2Closed.value = false;
  simState.value = "idle";
}

onUnmounted(() => {
  if (timerId) clearInterval(timerId);
});
</script>

<template>
  <div class="circuit-root">
    <!-- 电路 SVG：占满全宽 -->
    <div class="circuit-svg-wrap">
      <svg viewBox="0 0 580 220" class="circuit-svg">
        <!-- 地线 -->
        <line x1="60" y1="180" x2="500" y2="180" stroke="#475569" stroke-width="2.5" />

        <!-- 电池 E -->
        <line x1="60" y1="40" x2="60" y2="98" stroke="#475569" stroke-width="2.5" />
        <line x1="60" y1="122" x2="60" y2="180" stroke="#475569" stroke-width="2.5" />
        <line x1="44" y1="98" x2="76" y2="98" stroke="#ef4444" stroke-width="3" />
        <line x1="52" y1="106" x2="68" y2="106" stroke="#475569" stroke-width="5" />
        <line x1="44" y1="114" x2="76" y2="114" stroke="#ef4444" stroke-width="3" />
        <line x1="52" y1="122" x2="68" y2="122" stroke="#475569" stroke-width="5" />
        <text
          x="24"
          y="22"
          font-size="14"
          font-weight="950"
          font-family="Times New Roman, STIX Two Text, serif"
          font-style="italic"
          fill="#ef4444"
          text-anchor="start"
        >
          E=8V
        </text>

        <!-- 上部导线 -->
        <line x1="60" y1="40" x2="240" y2="40" stroke="#475569" stroke-width="2.5" />

        <!-- C₂ 上极板连线 -->
        <line x1="150" y1="110" x2="150" y2="99" stroke="#475569" stroke-width="2.5" />
        <line x1="150" y1="99" x2="240" y2="99" stroke="#475569" stroke-width="2.5" />

        <!-- S1 导轨弧线 -->
        <path
          d="M 240 41 A 85 85 0 0 0 240 99"
          fill="none"
          stroke="#334155"
          stroke-dasharray="3,3"
          stroke-width="2"
        />

        <!-- S1 触点 1（充电） -->
        <g class="cursor-pointer group" @click="toggleS1('1')">
          <circle cx="240" cy="41" r="18" fill="transparent" />
          <circle
            cx="240"
            cy="41"
            r="11"
            fill="#1e293b"
            :stroke="s1Pos === '1' ? '#ef4444' : '#475569'"
            stroke-width="2.5"
          />
          <text x="240" y="47" font-size="15" font-weight="950" fill="#ef4444" text-anchor="middle">
            1
          </text>
        </g>

        <!-- S1 触点 0（断开） -->
        <g class="cursor-pointer group" @click="toggleS1('open')">
          <circle cx="235" cy="70" r="18" fill="transparent" />
          <circle
            cx="235"
            cy="70"
            r="11"
            fill="#1e293b"
            :stroke="s1Pos === 'open' ? '#94a3b8' : '#475569'"
            stroke-width="2.5"
          />
          <text x="235" y="76" font-size="15" font-weight="950" fill="#94a3b8" text-anchor="middle">
            0
          </text>
        </g>

        <!-- S1 触点 2（共享） -->
        <g class="cursor-pointer group" @click="toggleS1('2')">
          <circle cx="240" cy="99" r="18" fill="transparent" />
          <circle
            cx="240"
            cy="99"
            r="11"
            fill="#1e293b"
            :stroke="s1Pos === '2' ? '#a78bfa' : '#475569'"
            stroke-width="2.5"
          />
          <text
            x="240"
            y="105"
            font-size="15"
            font-weight="950"
            fill="#a78bfa"
            text-anchor="middle"
          >
            2
          </text>
        </g>

        <!-- S1 枢轴 -->
        <circle cx="320" cy="70" r="8" fill="#e2a846" stroke="#101827" stroke-width="2" />
        <text x="320" y="47" font-size="14" font-weight="950" fill="#e2a846" text-anchor="middle">
          S₁
        </text>

        <!-- S1 刀臂 -->
        <line
          x1="320"
          y1="70"
          :x2="s1Target.x"
          :y2="s1Target.y"
          stroke="#e2a846"
          stroke-width="5"
          stroke-linecap="round"
          class="switch-arm transition-all duration-300 ease-in-out cursor-pointer"
          @click="toggleS1(s1Pos === 'open' ? '1' : s1Pos === '1' ? '2' : 'open')"
        />

        <!-- S₂ 接线柱3（断开，点击切换） -->
        <g class="cursor-pointer" @click="toggleS2(false)">
          <circle cx="242" cy="169" r="18" fill="transparent" />
          <circle
            cx="242"
            cy="169"
            r="11"
            fill="#1e293b"
            :stroke="!s2Closed ? '#94a3b8' : '#475569'"
            stroke-width="2.5"
          />
          <text
            x="242"
            y="175"
            font-size="15"
            font-weight="950"
            fill="#94a3b8"
            text-anchor="middle"
          >
            3
          </text>
        </g>

        <!-- S₂ 接线柱4（接地，点击切换） -->
        <g class="cursor-pointer" @click="toggleS2(true)">
          <circle cx="205" cy="178" r="18" fill="transparent" />
          <circle
            cx="205"
            cy="178"
            r="11"
            fill="#1e293b"
            :stroke="s2Closed ? '#34d399' : '#475569'"
            stroke-width="2.5"
          />
          <text
            x="205"
            y="184"
            font-size="15"
            font-weight="950"
            fill="#34d399"
            text-anchor="middle"
          >
            4
          </text>
        </g>

        <!-- S₂ 刀臂（枢轴在205,99，向右下~28°/正下摆动） -->
        <g>
          <path
            d="M 242 169 A 79 79 0 0 1 205 178"
            fill="none"
            stroke="#334155"
            stroke-dasharray="2,2"
            stroke-width="1.5"
          />
          <circle cx="205" cy="99" r="6" fill="#e2a846" stroke="#101827" stroke-width="1.5" />
          <line
            x1="205"
            y1="99"
            :x2="s2Target.x"
            :y2="s2Target.y"
            stroke="#e2a846"
            stroke-width="4"
            stroke-linecap="round"
            class="transition-all duration-200 ease-out"
          />
          <text x="193" y="92" font-size="14" font-weight="950" fill="#e2a846" text-anchor="start">
            S₂
          </text>
        </g>

        <!-- C₂ -->
        <g>
          <line x1="150" y1="130" x2="150" y2="180" stroke="#475569" stroke-width="2.5" />
          <line
            x1="120"
            y1="110"
            x2="180"
            y2="110"
            :stroke="bUpperColor"
            stroke-width="5"
            stroke-linecap="round"
            class="transition-colors duration-300"
          />
          <line
            x1="120"
            y1="130"
            x2="180"
            y2="130"
            :stroke="bLowerColor"
            stroke-width="5"
            stroke-linecap="round"
            class="transition-colors duration-300"
          />
          <text
            x="150"
            y="155"
            font-size="13"
            font-weight="950"
            fill="#60a5fa"
            text-anchor="middle"
          >
            C₂
          </text>
          <text
            v-for="x in chargeXsC2"
            :key="'+' + x"
            :x="x"
            y="115"
            style="font-size: 10px"
            font-weight="950"
            fill="#f87171"
            text-anchor="middle"
            dominant-baseline="central"
          >
            +
          </text>
          <text
            v-for="x in chargeXsC2"
            :key="'-' + x"
            :x="x"
            y="125"
            style="font-size: 10px"
            font-weight="950"
            fill="#60a5fa"
            text-anchor="middle"
            dominant-baseline="central"
          >
            −
          </text>
        </g>

        <!-- S1 → C₁ 导线 -->
        <line x1="320" y1="70" x2="420" y2="70" stroke="#475569" stroke-width="2.5" />
        <line x1="420" y1="70" x2="420" y2="110" stroke="#475569" stroke-width="2.5" />

        <!-- C₁ -->
        <g>
          <line x1="420" y1="130" x2="420" y2="180" stroke="#475569" stroke-width="2.5" />
          <line
            x1="390"
            y1="110"
            x2="450"
            y2="110"
            :stroke="aUpperColor"
            stroke-width="5"
            stroke-linecap="round"
            class="transition-colors duration-300"
          />
          <line
            x1="390"
            y1="130"
            x2="450"
            y2="130"
            :stroke="aLowerColor"
            stroke-width="5"
            stroke-linecap="round"
            class="transition-colors duration-300"
          />
          <text
            x="420"
            y="155"
            font-size="13"
            font-weight="950"
            fill="#34d399"
            text-anchor="middle"
          >
            C₁
          </text>
          <text
            v-for="x in chargeXsC1"
            :key="'+' + x"
            :x="x"
            y="115"
            style="font-size: 10px"
            font-weight="950"
            fill="#f87171"
            text-anchor="middle"
            dominant-baseline="central"
          >
            +
          </text>
          <text
            v-for="x in chargeXsC1"
            :key="'-' + x"
            :x="x"
            y="125"
            style="font-size: 10px"
            font-weight="950"
            fill="#60a5fa"
            text-anchor="middle"
            dominant-baseline="central"
          >
            −
          </text>
        </g>

        <!-- 电压表 V（与 C₁ 并联） -->
        <line x1="420" y1="70" x2="500" y2="70" stroke="#475569" stroke-width="2.5" />
        <line x1="500" y1="70" x2="500" y2="95" stroke="#475569" stroke-width="2.5" />
        <line x1="500" y1="145" x2="500" y2="180" stroke="#475569" stroke-width="2.5" />
        <g class="cursor-pointer" @click="performReset">
          <circle cx="500" cy="120" r="20" fill="#1e293b" stroke="#34d399" stroke-width="2.5" />
          <text
            x="500"
            y="128"
            font-size="18"
            font-weight="950"
            font-family="Times New Roman, STIX Two Text, serif"
            font-style="italic"
            fill="#34d399"
            text-anchor="middle"
          >
            V
          </text>
        </g>
      </svg>

      <!-- 电压表读数 -->
      <div class="voltage-badge">
        <span class="voltage-value">{{ qA.toFixed(2) }}</span>
        <span class="voltage-unit">V</span>
      </div>

      <!-- 极性图例 -->
      <div class="flex items-center justify-center gap-3 mb-2">
        <span class="inline-flex items-center gap-2 text-sm font-medium opacity-80"
          ><span class="inline-block w-3.5 h-3.5 rounded-sm" style="background: #f87171"></span>
          正电（红）</span
        >
        <span class="inline-flex items-center gap-2 text-sm font-medium opacity-80"
          ><span class="inline-block w-3.5 h-3.5 rounded-sm" style="background: #60a5fa"></span>
          负电（蓝）</span
        >
      </div>
    </div>

    <!-- 底部状态栏 -->
    <div class="status-bar">
      <div class="status-item">
        <span class="status-key">S₁</span>
        <span
          :class="
            s1Pos === '1' ? 'text-rose-400' : s1Pos === '2' ? 'text-purple-400' : 'text-slate-400'
          "
          class="status-val"
          >{{ s1StatusText }}</span
        >
      </div>
      <div class="status-item">
        <span class="status-key">S₂</span>
        <span :class="s2Closed ? 'text-emerald-400' : 'text-slate-500'" class="status-val">{{
          s2StatusText
        }}</span>
      </div>
      <button @click="performReset" class="reset-btn">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2.5"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8" />
          <path d="M3 3v5h5" />
        </svg>
        重置
      </button>
    </div>
  </div>
</template>

<style scoped>
.circuit-root {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
}

/* ── SVG 容器：电路占满空间 ── */
.circuit-svg-wrap {
  position: relative;
  background: rgba(15, 23, 42, 0.5);
  border-radius: 0.75rem;
  border: 1px solid rgba(51, 65, 85, 0.3);
}

.circuit-svg {
  display: block;
  width: 100%;
  height: auto;
}

.circuit-svg text {
  user-select: none !important;
  pointer-events: none !important;
}

/* ── 电压表读数覆盖层 ── */
.voltage-badge {
  position: absolute;
  top: 0.5rem;
  right: 0.75rem;
  display: flex;
  align-items: baseline;
  gap: 0.3rem;
  background: rgba(15, 23, 42, 0.85);
  border: 1px solid rgba(52, 211, 153, 0.35);
  border-radius: 0.5rem;
  padding: 0.35rem 0.75rem;
  backdrop-filter: blur(8px);
  pointer-events: none;
}

.voltage-label {
  font-size: 0.7rem;
  font-weight: 950;
  color: #34d399;
  opacity: 0.7;
}

.voltage-value {
  font-size: 1.4rem;
  font-weight: 900;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, monospace;
  color: #6ee7b7;
  text-shadow: 0 0 14px rgba(16, 185, 129, 0.4);
  line-height: 1;
}

.voltage-unit {
  font-size: 0.8rem;
  font-weight: 700;
  color: #34d399;
}

/* ── 底部状态栏 ── */
.status-bar {
  display: flex;
  align-items: center;
  gap: 1.25rem;
  background: rgba(15, 23, 42, 0.7);
  border-radius: 0.75rem;
  border: 1px solid rgba(51, 65, 85, 0.3);
  padding: 0.5rem 1rem;
}

.status-item {
  display: flex;
  align-items: center;
  gap: 0.4rem;
}

.status-key {
  font-size: 0.65rem;
  font-weight: 900;
  color: #e2a846;
  text-transform: uppercase;
}

.status-val {
  font-size: 0.75rem;
  font-weight: 800;
  text-transform: uppercase;
  letter-spacing: 0.02em;
}

.reset-btn {
  margin-left: auto;
  display: flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.35rem 0.85rem;
  background: rgba(185, 28, 28, 0.25);
  border: 1px solid rgba(239, 68, 68, 0.3);
  border-radius: 0.5rem;
  color: #f87171;
  font-size: 0.75rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.2s;
}
.reset-btn:hover {
  background: rgba(185, 28, 28, 0.45);
  border-color: rgba(239, 68, 68, 0.5);
  color: #fca5a5;
}

/* ── 交互 ── */
.switch-arm {
  cursor: pointer;
}
</style>
