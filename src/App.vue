<template>
  <div class="wedding-invite">
    <header class="wedding-invite__music">
      <button
          class="music-toggle"
          @click.stop="togglePlayback"
          :aria-pressed="isPlaying"
          aria-label="Play / Pause music"
          title="Play / Pause"
      >
        {{ isPlaying ? "⏸" : "▶" }}
      </button>

      <audio
          ref="audioRef"
          :src="musicSrc"
          autoplay
          muted
          loop
          playsinline
          preload="auto"
      ></audio>
    </header>

    <main class="wedding-invite__content">
      <section class="wedding-invite__photo">
        <img src="./assets/1.jpg" alt="Фото пары" class="photo" />
      </section>

      <p class="wedding-invite__message">Разделите этот чудесный день с нами!</p>

      <div class="wedding-invite__names">
        <div class="name name--groom">Элдияр</div>
        <div class="ampersand">&</div>
        <div class="name name--bride">Алия</div>
      </div>

      <div class="vibes" style="font-size: 30px; margin-bottom: 20px;">Дорогие родные и друзья!</div>

      <section class="section">
  <span style="font-size: 16px; line-height: 25px">
    Совсем скоро в нашей жизни состоится торжественное событие —
    <span class="highlight">День нашей свадьбы</span>.
  </span>
        <div>
          <img alt="photo" src="./assets/5.jpg" width="90px"/>
        </div>
      </section>


      <section class="calendar-section">
        <h2 class="program-title">05.11.2025</h2>

        <div class="calendar">
          <div class="weekdays">
            <div v-for="day in weekdays" :key="day">{{ day }}</div>
          </div>

          <div class="days">
            <div v-for="(day, idx) in days" :key="idx"
                 :class="['day', { empty: day === '', heart: day === 5 }]">
              <span v-if="day">{{ day }}</span>
              <span v-if="day === 5" class="heart-icon">❤️</span>
            </div>
          </div>
        </div>
      </section>

      <section class="section" style="text-align: center;">
        В этот особенный день мы хотим оказаться в окружении самых близких и дорогих нам людей.
      </section>


        <section class="program">
          <h2 class="program-title">Программа&nbsp;&nbsp;торжества</h2>

          <div class="timeline">
            <div class="event" v-for="item in events" :key="item.time">
              <div class="event-left">
                <div class="event-text" style="word-break: break-word; width: 150px; font-weight: 400">{{ item.title }}</div>
                <b class="event-time">{{ item.time }}</b>
              </div>

              <div class="line"></div>

              <div class="event-right">
                <img :src="item.img" alt="" class="circle" />
              </div>
            </div>
          </div>
        </section>


      <section class="program">
          <h2 class="program-title">Место проведения</h2>

          <div style="display: flex; flex-flow: column;gap:10px">
            <span>Bellagio Premium</span>
            <span style="margin-bottom: 10px;">ул. Токтогула, 125/1, 4-этаж</span>
          </div>


      <div>
        <a href="https://2gis.kg/bishkek/firm/70000001047984986">
         <img src="./assets/2gis.png" width="90px"/>
        </a>



        <div style="font-family: 'Great Vibes';    font-size: 25px; position: relative">
          нажми на меня

          <img src="./assets/arrow.png" alt="arrow" style=" position:  absolute;rotate: 180deg; width: 70px; top: -35px;
    right: -13px;"/>
        </div>
      </div>
        </section>

      <section>
          <div class="countdown">
            <div class="time-block">
              <div class="number">{{ dayz }}</div>
              <div class="label">дней</div>
            </div>

            <div class="divider"></div>

            <div class="time-block">
              <div class="number">{{ hours }}</div>
              <div class="label">часов</div>
            </div>

            <div class="divider"></div>

            <div class="time-block">
              <div class="number">{{ minutes }}</div>
              <div class="label">минут</div>
            </div>

            <div class="divider"></div>

            <div class="time-block">
              <div class="number">{{ seconds }}</div>
              <div class="label">секунд</div>
            </div>
          </div>
      </section>


      <section>
        <h2 class="program-title" style="text-align: center">До встречи на нашей свадьбе!</h2>

        <div style="text-align: center">
        <img src="./assets/2.jpg" width="90%" />
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount,onUnmounted } from "vue";
import musicSrc from "./assets/yildiz.mp3";
import sd from './assets/7.jpg'
import sd1 from './assets/8.jpg'
import sd2 from './assets/9.jpg'

/* Refs */
const audioRef = ref(null);
const isPlaying = ref(false);

/* Флаги состояния */
const hasStarted = ref(false); // уже успешно стартовали
const isStarting = ref(false); // сейчас в процессе старта

/**
 * Плавный fade-in громкости (requestAnimationFrame)
 * @param {HTMLAudioElement} audio
 * @param {number} duration ms
 */
const fadeIn = (audio, duration = 1500) => {
  try {
    audio.volume = 0;
  } catch (e) {
    // защищаемся на случай, если элемент недоступен
  }
  const start = performance.now();
  function step(now) {
    const t = Math.min((now - start) / duration, 1);
    audio.volume = t; // линейный рост от 0 до 1
    if (t < 1) requestAnimationFrame(step);
  }
  requestAnimationFrame(step);
};


// Целевая дата
const targetDate = new Date('2025-11-05T00:00:00')

// Создаем реактивные переменные
const dayz = ref(0)
const hours = ref(0)
const minutes = ref(0)
const seconds = ref(0)

let timer = null

const updateCountdown = () => {
  const now = new Date()
  const diff = targetDate - now

  if (diff <= 0) {
    // Время вышло
    dayz.value = 0
    hours.value = 0
    minutes.value = 0
    seconds.value = 0
    clearInterval(timer)
    return
  }

  dayz.value = Math.floor(diff / (1000 * 60 * 60 * 24))
  hours.value = Math.floor((diff / (1000 * 60 * 60)) % 24)
  minutes.value = Math.floor((diff / (1000 * 60)) % 60)
  seconds.value = Math.floor((diff / 1000) % 60)
}

onMounted(() => {
  updateCountdown()
  timer = setInterval(updateCountdown, 1000)
  window.addEventListener("touchstart", onTouch, { once: true, passive: true });
})

onUnmounted(() => {
  window.removeEventListener("touchstart", onTouch);
  clearInterval(timer)
})

const onTouch = () => {
  const audio = audioRef.value;
  if (!audio || hasStarted.value) return;

  hasStarted.value = true;
  audio.muted = false;  // сразу снимаем muted
  audio.play().catch(err => console.log("play error", err));
  fadeIn(audio, 1500);
  isPlaying.value = true;
  removeGestureListeners();
};

const events = [
  {
    title: "Фуршет регистрация",
    time: "17:00",
    img: sd,
  },
  {
    title: "Начало программы",
    time: "18:00",
    img:sd1,
  },
  {
    title: "Торт",
    time: "22:00",
    img: sd2,
  },
];
/**
 * Попытаться запустить аудио в результате пользовательского жеста.
 * Логика:
 *  - play() в muted (браузер позволит)
 *  - затем снимаем muted и делаем fadeIn
 */
const tryStartAudio = async () => {
  if (hasStarted.value || isStarting.value) return;
  const audio = audioRef.value;
  if (!audio) return;

  isStarting.value = true;

  try {
    // запускаем в muted, чтобы браузер позволил воспроизведение
    audio.muted = true;
    audio.volume = 0;
    await audio.play();

    // теперь "включаем" звук и делаем плавный fade-in
    audio.muted = false;
    fadeIn(audio, 1500);

    isPlaying.value = true;
    hasStarted.value = true;
    isStarting.value = false;

    // удачный старт — снимаем слушатели (они зарегистрированы с once, но это лишний шаг)
    removeGestureListeners();
    // debug
    // console.log("Audio started by user gesture");
  } catch (err) {
    // если воспроизведение всё ещё заблокировано — оставляем возможность повторной попытки
    isStarting.value = false;
    // console.warn("tryStartAudio failed:", err);
  }
};

/**
 * Тоггл воспроизведения — кнопка управления
 */
const togglePlayback = async () => {
  const audio = audioRef.value;
  if (!audio) return;

  if (isPlaying.value) {
    audio.pause();
    isPlaying.value = false;
  } else {
    try {
      await audio.play();
      // если громкость была 0 (редкий кейс), сделать короткий fade-in
      if (audio.volume === 0) fadeIn(audio, 800);
      isPlaying.value = true;
    } catch (err) {
      // если play() из кнопки падает — попросим пользователя выполнить явный жест (pointerdown/tap)
      // console.warn("togglePlayback play failed:", err);
    }
  }
};

/* ---- обработчики жестов ---- */
const onPointerGesture = () => tryStartAudio();
const onWheelOrScroll = () => tryStartAudio();

/* Регистрация / снятие слушателей */
function addGestureListeners() {
  // pointerdown / touchstart / keydown — самые надёжные
  window.addEventListener("pointerdown", onPointerGesture, { once: true, passive: true });
  window.addEventListener("touchstart", onPointerGesture, { once: true, passive: true });
  window.addEventListener("keydown", onPointerGesture, { once: true, passive: true });

  // запасные — иногда срабатывать не будут, но не мешают
  window.addEventListener("wheel", onWheelOrScroll, { once: true, passive: true });
  window.addEventListener("scroll", onWheelOrScroll, { once: true, passive: true });
}

function removeGestureListeners() {
  window.removeEventListener("pointerdown", onPointerGesture);
  window.removeEventListener("touchstart", onPointerGesture);
  window.removeEventListener("keydown", onPointerGesture);
  window.removeEventListener("wheel", onWheelOrScroll);
  window.removeEventListener("scroll", onWheelOrScroll);
}

const weekdays = ["ПН", "ВТ", "СР", "ЧТ", "ПТ", "СБ", "ВС"];

// 1 ноября 2025 — это суббота (значит, перед ним 5 пустых ячеек)
const days = [
  "", "", "", "", "",
  1, 2,
  3, 4, 5, 6, 7, 8, 9,
  10, 11, 12, 13, 14, 15, 16,
  17, 18, 19, 20, 21, 22, 23,
  24, 25, 26, 27, 28, 29, 30
];

onMounted(() => {
  addGestureListeners();
});

onBeforeUnmount(() => {
  removeGestureListeners();
});
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Great+Vibes&family=Montserrat:ital,wght@0,100..900;1,100..900&family=Pattaya&display=swap');

.highlight {
  font-family: 'Great Vibes', cursive; /* или любой другой шрифт */
  font-size: 25px;
}

.countdown {
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: Arial, sans-serif;
  font-size: 36px;
}

.time-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 0 10px;
}

.number {
  font-weight: bold;
}

.label {
  font-size: 14px;
  color: #555;
}

/* Вертикальная линия */
.divider {
  width: 1px;
  height: 50px;
  background-color: #000000;
  margin: 0 10px;
}

.calendar-section {
  margin-top: 40px;
  text-align: center;
  font-family: "Montserrat", sans-serif;
  color: #000000;
}

.calendar {
  border-radius: 16px;
  padding: 16px;
  display: inline-block;
}

.weekdays,
.days {
  display: grid;
  grid-template-columns: repeat(7, 40px);
  justify-content: center;
  gap: 6px;
  font-weight: 300;
}

.weekdays div {
  font-weight: 600;
  font-size: 14px;
  color: #000000;
}

.day {
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 8px;
  position: relative;
}

.day.empty {
  background: transparent;
}

.day.heart {
  background: #ffe6e6;
  color: #d32f2f;
  font-weight: bold;
  position: relative;
}

.heart-icon {
  position: absolute;
  bottom: -5px;
  right: -7px;
  font-size: 14px;
}


.vibes {
  font-family: "Great Vibes", cursive;
  font-size: 25px;
}

.section {
  display: flex;
  padding: 23px;
  background: #46440f;
  color: #fff;
  font-weight: 300;
  font-family: Montserrat,serif;
  gap: 10px;
  align-items: center;

}


/* БЭМ-подобные классы, аккуратно и читаемо */
.wedding-invite {
  position: relative;
  width: 100%;
  min-height: 100vh;

  /* Фон */
  background-image: url("./assets/3.jpg");
  background-size: cover;        /* растягиваем изображение на весь экран */
  background-position: center;   /* центрирование */
  background-repeat: no-repeat;
  background-attachment: fixed;  /* фиксируем фон, чтобы не скроллился */

  display: flex;
  flex-direction: column;
  align-items: stretch;
}

.wedding-invite::before {
  content: "";
  position: fixed; /* фиксированный слой, чтобы оставался на месте */
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.6); /* регулируй прозрачность */
  pointer-events: none; /* чтобы клики проходили к контенту */
  z-index: 0;
}

/* Контент выше слоя фона */
.wedding-invite > * {
  position: relative;
  z-index: 1;
}

/* Музыкальный блок (правый верхний угол) */
.wedding-invite__music {
  display: flex;
}

.music-toggle {
  visibility: hidden;
}

/* Основной контент по центру */
.wedding-invite__content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.wedding-invite__photo {
  display: flex;
  justify-content: center;
  width: 100%;
}

.photo {
  width: 230px;
  height: 350px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  border-radius: 120px 120px 0 0;
  object-fit: cover;
}

.wedding-invite__message {
  font-family: "Great Vibes", cursive;
  font-size: 26px;
  text-align: center;
  margin-top: 15px;
}

/* Имена пары */
.wedding-invite__names {
  margin: 50px 0;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 24px;
}

.name {
  font-family: "Great Vibes", cursive;
  font-size: 60px;
  text-align: center;
  font-weight: 600;
}

.ampersand {
  font-size: 50px;
  color: #46440f;
}

.program {
  text-align: center;
  padding: 40px 0;
  font-family: "Montserrat", sans-serif;
}

.program-title {
  font-family: "Great Vibes", cursive;
  font-size: 34px;
  margin-bottom: 40px;
}

.timeline {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 60px;
  position: relative;
}

/* Одна пара: текст | линия | фото */
.event {
  display: flex;
  align-items: center;
  justify-content: space-around;
  gap: 40px;
  position: relative;
}

.event-left {
  text-align: right;
  font-weight: 400;
}

.event-time {
  font-size: 14px;
  margin-top: 4px;
}

.line {
  width: 1.5px;
  height: 100%;
  background-color: #ccc;
  position: relative;
}

.event-right {
  display: flex;
  justify-content: center;
  align-items: center;
}

.circle {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  object-fit: cover;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}

/* Соединительная линия (вертикальная) */
.timeline::before {
  content: "";
  position: absolute;
  left: 50%;
  top: 0;
  bottom: 0;
  width: 1px;
  background-color: #568300;
  transform: translateX(-50%);
  z-index: 0;
}
</style>
