<template>
  <div>
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
              <p>                Рыбатекст используется дизайнерами, проектировщиками и фронтендерами, когда нужно быстро заполнить макеты или прототипы содержимым. Это тестовый контент...
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

    <!-- Основной контент страницы Люди -->
    <div class="container">
      <div class="main">
        <div class="nav__block">
          <!-- Календарь (тот же код, что на главной) -->
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
                  :class="{ 'other-month': day.other, 'today': day.today }"
                >
                  {{ day.num }}
                </div>
              </div>
            </div>
          </div>

          <!-- Активные приглашения (тот же блок) -->
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

        <!-- Фильтр и карточки людей -->
        <div class="main__block">
          <div class="filtr">
            <div class="inp__filtr">
              <input type="text" placeholder="Поиск" />
              <select>
                <option>Все</option>
                <option selected>По алфавиту</option>
                <option>Рандомно ахах</option>
              </select>
            </div>
            <button>Добавить +</button>
          </div>

          <div class="person__block">
            <div class="person__block__item" v-for="person in people" :key="person.name">
              <div class="person__img">
                <img :src="person.img" :alt="person.name" />
                <p>{{ person.role }}</p>
              </div>
              <div class="person__info">
                <h2>{{ person.name }}</h2>
                <p>{{ person.desc }}</p>
                <div class="section__button">
                  <button>Пригласить</button>
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

// Профиль меню
const showProfile = ref(false)

// Календарь (тот же логика)
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

  for (let i = startIndex; i > 0; i--) {
    days.push({ num: daysPrev - i + 1, other: true, today: false })
  }

  const today = new Date()
  for (let d = 1; d <= daysInMonth; d++) {
    const isToday = d === today.getDate() && month === today.getMonth() && year === today.getFullYear()
    days.push({ num: d, other: false, today: isToday })
  }

  while (days.length < 42) {
    days.push({ num: days.length - (daysInMonth + startIndex) + 1, other: true, today: false })
  }

  return days
})

function prevMonth() { currentDate.value.setMonth(currentDate.value.getMonth() - 1) }
function nextMonth() { currentDate.value.setMonth(currentDate.value.getMonth() + 1) }

// Временные данные
const invitations = ref([
  { id: 1, title: 'Съёмки в первомайском', time: '10.00 - 12.00', person: 'Иванов И. В.', role: 'Фотограф' },
  { id: 2, title: 'Съёмки в первомайском', time: '10.00 - 12.00', person: 'Иванов И. В.', role: 'Фотограф' }
])

const people = ref([
  { name: 'Иван Иванов', role: 'Фотограф', desc: 'Высококачественная видеосъёмка и аэросъёмка: доступные цены, профессиональное оборудование и опытные специалисты', 
    img: '/_nuxt/assets/img/persone.png',
   },
  { name: 'Анастасия Ремогузина', role: 'Фотограф', desc: 'Высококачественная видеосъёмка и аэросъёмка: доступные цены, профессиональное оборудование и опытные специалисты', 
    img: '/_nuxt/assets/img/persone2.png',
   },
  { name: 'Иван Иванов', role: 'Фотограф', desc: 'Высококачественная видеосъёмка и аэросъёмка: доступные цены, профессиональное оборудование и опытные специалисты', 
    img: '/_nuxt/assets/img/persone.png',
   },
  { name: 'Анастасия Ремогузина', role: 'Фотограф', desc: 'Высококачественная видеосъёмка и аэросъёмка: доступные цены, профессиональное оборудование и опытные специалисты', 
    img: '/_nuxt/assets/img/persone2.png',
   },

])
</script>