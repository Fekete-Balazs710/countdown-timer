<template>
  <div class="wrap flex justify-center flex-col items-center h-screen">
    <h1 class="text-white">Time until <strong class="ml-2">Payday</strong></h1>

    <div
      v-show="!loading"
      class="countdown flex flex-col md:flex-row items-center"
    >
      <div
        class="bloc-time days flex flex-col md:block md:mr-12"
        data-init-value="1"
      >
        <span class="count-title">Days</span>
        <div>
          <div class="figure days days-1">
            <span class="top">0</span>
            <span class="top-back hidden"><span>0</span></span>
            <span class="bottom hidden">0</span>
            <span class="bottom-back hidden"><span>0</span></span>
          </div>
          <div class="figure days days-2">
            <span class="top">1</span>
            <span class="top-back hidden"><span>1</span></span>
            <span class="bottom hidden">1</span>
            <span class="bottom-back hidden"><span>1</span></span>
          </div>
        </div>
      </div>
      <div
        class="bloc-time hours flex flex-col md:block md:mr-12 mt-4 md:mt-0"
        data-init-value="24"
      >
        <span class="count-title">Hours</span>
        <div>
          <div class="figure hours hours-1">
            <span class="top">2</span>
            <span class="top-back hidden"><span>2</span></span>
            <span class="bottom hidden">2</span>
            <span class="bottom-back hidden"><span>2</span></span>
          </div>
          <div class="figure hours hours-2">
            <span class="top">4</span>
            <span class="top-back hidden"><span>4</span></span>
            <span class="bottom hidden">4</span>
            <span class="bottom-back hidden"><span>4</span></span>
          </div>
        </div>
      </div>
      <div
        class="bloc-time min flex flex-col md:block mt-4 md:mt-0 md:mr-12"
        data-init-value="0"
      >
        <span class="count-title">Minutes</span>
        <div>
          <div class="figure min-1">
            <span class="top">0</span>
            <span class="top-back hidden"><span>0</span></span>
            <span class="bottom hidden">0</span>
            <span class="bottom-back hidden"><span>0</span></span>
          </div>
          <div class="figure min-2">
            <span class="top">0</span>
            <span class="top-back hidden"><span>0</span></span>
            <span class="bottom hidden">0</span>
            <span class="bottom-back hidden"><span>0</span></span>
          </div>
        </div>
      </div>
      <div
        class="bloc-time sec flex md:block flex-col mt-4 md:mt-0"
        data-init-value="0"
      >
        <span class="count-title">Seconds</span>
        <div>
          <div class="figure sec-1">
            <span class="top">0</span>
            <span class="top-back hidden"><span>0</span></span>
            <span class="bottom hidden">0</span>
            <span class="bottom-back hidden"><span>0</span></span>
          </div>
          <div class="figure sec-2">
            <span class="top">0</span>
            <span class="top-back hidden"><span>0</span></span>
            <span class="bottom hidden">0</span>
            <span class="bottom-back hidden"><span>0</span></span>
          </div>
        </div>
      </div>
    </div>

    <div v-show="loading" role="status" class="max-w-sm animate-pulse">
      <div class="flex flex-col md:flex-row gap-6 justify-center">
        <div class="flex flex-row gap-2">
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
        </div>
        <div class="flex flex-row gap-2">
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
        </div>
        <div class="flex flex-row gap-2">
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
        </div>
        <div class="flex flex-row gap-2">
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
          <div class="h-28 bg-neutral-700 rounded-lg w-24 mb-4"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { gsap, Quart } from "gsap";

const loading = ref(true);

const calculateInitialTotalSeconds = () => {
  const now = new Date();
  const currentYear = now.getFullYear();
  const currentMonth = now.getMonth();
  const nextMonth = (currentMonth + 1) % 12;
  const nextMonthYear = nextMonth === 0 ? currentYear + 1 : currentYear;

  const targetDate = new Date(nextMonthYear, nextMonth, 15, 0, 0, 0);
  const differenceInSeconds = Math.floor((targetDate - now) / 1000);

  return differenceInSeconds;
};

const initialDays = 0;
const initialHours = 0;
const initialMinutes = 0;
const initialSeconds = 0;

const totalSeconds = ref(calculateInitialTotalSeconds());

const days = ref(["0", "0"]);
const hours = ref(["0", "0"]);
const minutes = ref(["0", "0"]);
const seconds = ref(["0", "0"]);
let countdownInterval = null;

function startCountdown() {
  countdownInterval = setInterval(() => {
    if (totalSeconds.value > 0) {
      totalSeconds.value--;
      const d = Math.floor(totalSeconds.value / (3600 * 24));
      const h = Math.floor((totalSeconds.value % (3600 * 24)) / 3600);
      const m = Math.floor((totalSeconds.value % 3600) / 60);
      const s = totalSeconds.value % 60;
      updateTime(days, d, "days");
      updateTime(hours, h, "hours");
      updateTime(minutes, m, "min");
      updateTime(seconds, s, "sec");
    } else {
      clearInterval(countdownInterval);
    }
  }, 1000);

  console.log("countdownInterval totalSeconds: ", totalSeconds.value);

  return 1;
}

const updateTime = (unitRef, value, unit) => {
  const valStr = value.toString().padStart(2, "0");
  animateFigure(
    unitRef.value[0],
    valStr.charAt(0),
    `.bloc-time.${unit} .figure.${unit}-1`
  );
  animateFigure(
    unitRef.value[1],
    valStr.charAt(1),
    `.bloc-time.${unit} .figure.${unit}-2`
  );
  unitRef.value = [valStr.charAt(0), valStr.charAt(1)];
};

const animateFigure = (oldValue, newValue, selector) => {
  const $el = document.querySelector(selector);
  if ($el) {
    const $top = $el.querySelector(".top");
    const $bottom = $el.querySelector(".bottom");
    const $backTop = $el.querySelector(".top-back span");
    const $backBottom = $el.querySelector(".bottom-back span");

    $backTop.innerHTML = newValue;
    $backBottom.innerHTML = newValue;

    gsap.to($top, {
      rotationX: "-180deg",
      transformPerspective: 300,
      ease: Quart.easeOut,
      duration: 0.8,
      onComplete: () => {
        $top.innerHTML = newValue;
        $bottom.innerHTML = newValue;
        gsap.set($top, { rotationX: 0 });
      },
    });

    gsap.to($backTop, {
      rotationX: 0,
      transformPerspective: 300,
      ease: Quart.easeOut,
      duration: 0.8,
      clearProps: "all",
    });
  }
};

onMounted(() => {
  (async () => {
    await startCountdown();
    setTimeout(() => {
      loading.value = false;
    }, 2000);
  })();
});

onBeforeUnmount(() => {
  clearInterval(countdownInterval);
});
</script>

<style>
body {
  background-color: #171717;
}

a {
  text-decoration: none;
  color: white;
}

h1 {
  margin-bottom: 60px;
  text-align: center;
  font: 300 2.25em "Lato";
  text-transform: uppercase;
}

h1 strong {
  font-weight: 400;
  color: #34d399;
}

h2 {
  text-align: center;
  font: 300 0.7em "Lato";
  text-transform: uppercase;
}

h2 strong {
  font-weight: 400;
}

.countdown {
  margin: 0 auto;
}

.countdown .bloc-time {
  float: left;
  text-align: center;
}

.countdown .bloc-time:last-child {
  margin-right: 0;
}

.countdown .count-title {
  display: block;
  margin-bottom: 15px;
  font: normal 0.94em "Lato";
  color: white;
  text-transform: uppercase;
}

.countdown .figure {
  position: relative;
  float: left;
  height: 110px;
  width: 100px;
  margin-right: 10px;
  background-color: #262626;
  border-radius: 8px;
  box-shadow: 0 3px 4px 0 rgba(0, 0, 0, 0.2),
    inset 2px 4px 0 0 rgba(255, 255, 255, 0.08);
}

.countdown .figure:last-child {
  margin-right: 0;
}

.countdown .figure > span {
  position: absolute;
  left: 0;
  right: 0;
  margin: auto;
  font: normal 5.94em/107px "Lato";
  font-weight: 700;
  color: #34d399;
}

.countdown .figure .top,
.countdown .figure .bottom-back {
  position: relative;
}

.countdown .figure .top:after,
.countdown .figure .bottom-back:after {
  content: "";
  position: absolute;
  z-index: -1;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.countdown .figure .top {
  z-index: 3;
  transform-origin: 50% 100%;
  transform: perspective(200px);
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
}

.countdown .figure .bottom {
  z-index: 1;
  position: relative;
}

.countdown .figure .bottom:before {
  content: "";
  position: absolute;
  display: block;
  top: 0;
  left: 0;
  width: 100%;
  height: 50%;
  background-color: rgba(0, 0, 0, 0.02);
}

.countdown .figure .bottom-back {
  z-index: 2;
  top: 0;
  height: 50%;
  overflow: hidden;
  background-color: #f7f7f7;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
}

.countdown .figure .bottom-back span {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  margin: auto;
}

.countdown .figure .top,
.countdown .figure .top-back {
  height: 50%;
  overflow: hidden;
  backface-visibility: hidden;
}

.countdown .figure .top-back {
  z-index: 4;
  bottom: 0;
  background-color: #fff;
  transform-origin: 50% 0;
  transform: perspective(200px) rotateX(180deg);
  border-bottom-left-radius: 10px;
  border-bottom-right-radius: 10px;
}

.countdown .figure .top-back span {
  position: absolute;
  top: -100%;
  left: 0;
  right: 0;
  margin: auto;
}
</style>
