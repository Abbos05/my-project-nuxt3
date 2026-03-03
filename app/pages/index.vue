<template>
  <div>
    <!-- Header (временный, потом вынесем в компонент) -->
    <header class="header">
      <div class="header__block">
        <div class="container">
          <div class="info__header">
            <div class="header__title">
              <img src="~/assets/img/redact.png" alt="redact" />
              <h1>СБР</h1>
              <p>Сервис бронирования ресурсов</p>
            </div>

            <div class="btn__header">
              <button>Вход</button>
              <button>Регистрация</button>
            </div>

            <div class="hedaer__description">
              <p>
                Рыбатекст используется дизайнерами, проектировщиками и фронтендерами, когда нужно быстро заполнить макеты или прототипы содержимым. Это тестовый контент...
              </p>
            </div>
          </div>
        </div>

        <nav>
          <div class="container">
            <a href="/">
              <img src="~/assets/img/Group.png" alt="img__header_redact" />
            </a>

            <ul>
              <li><NuxtLink to="/">Аппаратура</NuxtLink></li>
              <li><NuxtLink to="/person">Люди</NuxtLink></li>
              <li><a href="#">Пространство</a></li>

              <li class="profile-item">
                <a href="#" @click.prevent="showProfile = !showProfile">
                  Профиль
                  <img src="~/assets/img/profile.png" alt="profile" />
                </a>

                <div class="profile-menu" :class="{ active: showProfile }">
                  <div class="profile-info">
                    <h4>Иван Иванов <span>⚙</span></h4>
                    <p>example@yandex.ru</p>
                  </div>
                  <button class="logout-btn">Выход</button>
                </div>
              </li>
            </ul>
          </div>
        </nav>
      </div>
    </header>

    <!-- Основной контент -->
    <div class="container">
      <div class="main">
        <div class="nav__block">
          <!-- Календарь -->
          <div class="calendar__block">
            <div class="calendar">
              <div class="calendar-header">
                <button @click="prevMonth">&#10094;</button>
                <h2>{{ monthYear }}</h2>
                <button @click="nextMonth">&#10095;</button>
              </div>
              <div class="calendar-days">
                <div>Пн</div><div>Вт</div><div>Ср</div><div>Чт</div>
                <div>Пт</div><div>Сб</div><div>Вс</div>
              </div>
              <div class="calendar-dates">
                <div
                  v-for="(day, index) in calendarDays"
                  :key="index"
                  :class="{
                    'other-month': day.other,
                    'today': day.today
                  }"
                >
                  {{ day.num }}
                </div>
              </div>
            </div>
          </div>

          <!-- Активные приглашения (временные данные) -->
          <div class="invition__block">
            <p class="invition">Активные приглашение</p>

            <div class="invition__item" v-for="inv in invitations" :key="inv.id">
              <p>{{ inv.title }}</p>
              <div class="invition__item__list">
                <div>{{ inv.time }}</div>
                <div>{{ inv.person }}</div>
                <div>{{ inv.role }}</div>
              </div>
              <div class="section__button">
                <button>Подробнее</button>
                <button>Отклонить</button>
              </div>
            </div>
          </div>
        </div>

        <!-- Основные блоки (оборудование/коворкинги) -->
        <div class="main__block">
          <div class="main__block__item" v-for="item in items" :key="item.id">
            <div class="section__block">
              <div class="section__header__block">
                <img :src="item.img" alt="section" />
                <div class="section__info__img">
                  <p>фото</p>
                  <p>Информация</p>
                </div>
              </div>
            </div>

            <div class="section__time">
              <div class="section__items">
                <div class="section__title">
                  <p>{{ item.title }}</p>
                </div>

                <div class="recording__time">
                  <div
                    v-for="slot in timeSlots"
                    :key="slot"
                    :class="{ active: item.activeSlots.includes(slot) }"
                  >
                    {{ slot }}
                  </div>
                </div>

                <div class="section__button">
                  <button>Забронировать</button>
                  <button>Подробнее</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>

import { ref, computed } from 'vue'

// ------------------- Профиль-меню -------------------
const showProfile = ref(false)

// ------------------- Календарь -------------------
const currentDate = ref(new Date())

const monthYear = computed(() => {
  const months = ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь']
  return `${months[currentDate.value.getMonth()]} ${currentDate.value.getFullYear()}`
})

const calendarDays = computed(() => {
  const year = currentDate.value.getFullYear()
  const month = currentDate.value.getMonth()
  const first = new Date(year, month, 1)
  const last = new Date(year, month + 1, 0)
  const prevLast = new Date(year, month, 0)

  const startIndex = (first.getDay() + 6) % 7
  const daysInMonth = last.getDate()
  const daysPrev = prevLast.getDate()

  const days = []

  // Предыдущий месяц
  for (let i = startIndex; i > 0; i--) {
    days.push({ num: daysPrev - i + 1, other: true, today: false })
  }

  // Текущий месяц
  const today = new Date()
  for (let d = 1; d <= daysInMonth; d++) {
    const isToday = d === today.getDate() && month === today.getMonth() && year === today.getFullYear()
    days.push({ num: d, other: false, today: isToday })
  }

  // Следующий месяц
  while (days.length < 42) {
    days.push({ num: days.length - (daysInMonth + startIndex) + 1, other: true, today: false })
  }

  return days
})

function prevMonth() { currentDate.value.setMonth(currentDate.value.getMonth() - 1) }
function nextMonth() { currentDate.value.setMonth(currentDate.value.getMonth() + 1) }

// ------------------- Временные данные -------------------
const invitations = ref([
  { id: 1, title: 'Съёмки в первомайском', time: '10.00 - 12.00', person: 'Иванов И. В.', role: 'Фотограф' },
  { id: 2, title: 'Съёмки в первомайском', time: '10.00 - 12.00', person: 'Иванов И. В.', role: 'Фотограф' }
])

const timeSlots = ['08.00-09.00', '09.00-10.00', '10.00-11.00', '11.00-12.00', '12.00-13.00', '13.00-14.00',
                   '14.00-15.00', '15.00-16.00', '16.00-17.00', '17.00-18.00', '18.00-19.00', '19.00-20.00']


const items = ref([
  {
    id: 1,
    title: 'Коворкинг ИРНИТУ',
    img: '/_nuxt/assets/img/section.png',
    activeSlots: ['10.00-11.00', '11.00-12.00', '14.00-15.00', '18.00-19.00']
  },
  {
    id: 2,
    title: 'Коворкинг ИРНИТУ',
    img: '/_nuxt/assets/img/section2.png',
    activeSlots: ['10.00-11.00', '11.00-12.00', '14.00-15.00', '17.00-18.00']
  }
])
</script>

<style scoped>
/* Здесь можно временно вставить кусок твоего CSS, который относится именно к этой странице */
/* Но лучше оставь глобальный в assets/css/main.css и подключи в nuxt.config.ts */
</style>