import { Table } from '~/.nuxt/components'; import { type } from 'os';
<template>
  <div>
    <h2 class="text-2xl text-center my-3">Work log - David</h2>
    <h2 class="text-2xl text-center my-3 mb-48">Live data</h2>
    <div v-if="!monthData.length" class="text-center p-12">
      <p class="text-xl">Loading statistics...</p>
    </div>
    <div v-show="monthData.length" class="report content">
      <div
        class="date-hour-chart relative h-fit min-h-[22rem] flex gap-3 items-end justify-center p-3 mt-8 w-full mx-auto"
      >
        <div class="w-10"></div>
        <div class="relative w-full h-[20rem] flex items-center justify-center">
          <div
            class="absolute left-1/2 bottom-0 -translate-x-1/2 flex items-end gap-2"
          >
            <div
              v-for="(hour, date) in monthDateHours.value"
              :key="date"
              :class="{ today: date == today }"
              class="z-10 cursor-pointer h-fit"
              :title="`Date: ${date}\nHours: ${hour}`"
            >
              <div class="flex flex-col items-center">
                <div
                  class="w-10 column bg-teal-300"
                  :style="`height:${monthDateHours.value[date] * 20 * UNIT}px;`"
                ></div>
                <p class="absolute z-100 mt-[-22px] duration">
                  {{ hour }}
                </p>
              </div>
              <div
                @mouseenter="state.showWeekDay = true"
                @mouseleave="state.showWeekDay = false"
                class="cursor-pointer"
              >
                <p class="text-center date bg-teal-600 text-gray-100">
                  {{ date.slice(-2) }}
                </p>
              </div>
              <div
                @mouseenter="state.showWeekDay = true"
                @mouseleave="state.showWeekDay = false"
                class="h-6 w-full cursor-pointer"
              >
                <p
                  class="text-center text-gray-600"
                  :class="{
                    'bg-yellow-100': ['Mon'].includes(
                      moment(date).format('ddd'),
                    ),
                    hidden: !state.showWeekDay,
                  }"
                >
                  {{ moment(date).format('ddd') }}
                </p>
              </div>
            </div>
          </div>
        </div>
        <div v-if="false" class="absolute ruler w-full">
          <div
            class="absolute w-full p-3 cursor-pointer"
            :style="`bottom:${BASE_HEIGHT + 20 * PERFECT * UNIT}px`"
            :title="'Perfect: ' + PERFECT"
          >
            <span>{{ PERFECT }}</span>
            <hr class="border border-1 border-blue-800" />
          </div>
          <div
            class="absolute w-full p-3 cursor-pointer"
            :style="`bottom:${BASE_HEIGHT + 20 * GREAT * UNIT}px`"
            :title="'Great: ' + GREAT"
          >
            <span>{{ GREAT }}</span>
            <hr class="border border-1 border-blue-800" />
          </div>
          <div
            class="absolute w-full p-3 cursor-pointer"
            :style="`bottom:${BASE_HEIGHT + 20 * MINIMUM * UNIT}px`"
            :title="'Minimum: ' + MINIMUM"
          >
            <span class="absolute left-[-6px] top-[2px]">{{ MINIMUM }}</span>
            <hr class="border border-1 border-gray-700" />
          </div>
          <div
            class="absolute w-full p-3 cursor-pointer"
            :style="`bottom:${BASE_HEIGHT + 20 * LOW * UNIT}px`"
            :title="'Low: ' + LOW"
          >
            <span>{{ LOW }}</span>
            <hr class="border border-1 border-blue-800" />
          </div>
          <div
            class="absolute w-full p-3 cursor-pointer"
            :style="`bottom:${BASE_HEIGHT + 20 * average * UNIT}px`"
            :title="'Avarage: ' + average"
          >
            <span class="absolute left-[-22px] top-[2px]">{{ average }}</span>
            <hr class="border border-1 border-yellow-300" />
          </div>
        </div>
      </div>
      <div class="text-center">
        <button class="rounded-full" @click="minusMonth">&lt;</button>
        <p>{{ moment(date).format('YYYY MMMM') }}</p>
        <input v-model="date" class="text-center w-[8rem] hidden" />
        <button class="rounded-full" @click="addMonth">&gt;</button>
      </div>
      <div class="text-center mt-3">
        <!-- <p>Minimum: {{ minimum + (minimum < 2 ? ' day' : ' days') }}</p>
            <p>Required: {{ required + (required < 2 ? ' day' : ' days') }}</p>
            <p>Perfect: {{ perfect + (perfect < 2 ? ' day' : ' days') }}</p> -->
        <p>Total: {{ total }}</p>
        <p>Average: {{ average }}</p>
      </div>
      <Table :data="monthTasks" />
    </div>
  </div>
</template>
<script setup>
import { query, upsert, getById } from '~~/static/db';
import moment from 'moment';
import { sumTime, cvTime, createTimestamp } from '~~/static/time';

const config = inject('config');
const GROUP = config.group;

const BASE_HEIGHT = 48;
const state = reactive({
  showWeekDay: false,
});

const today = moment().format('YYYY-MM-DD');

const date = ref(today);
// const { minimumRankedHours: MINIMUM } = inject("vars");

const METRIC = {
  [GROUP]: {
    UNIT: 5,
    LOW: 0.5,
    MINIMUM: 1.5,
    GREAT: 2.5,
    PERFECT: 3.5,
    COLOR: '#14b8a6',
  },
};

const UNIT = METRIC[GROUP].UNIT;

const LOW = METRIC[GROUP].LOW;
const MINIMUM = METRIC[GROUP].MINIMUM;
const GREAT = METRIC[GROUP].GREAT;
const PERFECT = METRIC[GROUP].PERFECT;

function addMonth() {
  date.value = moment(date.value).add(1, 'month').format('YYYY-MM-DD');
}
function minusMonth() {
  date.value = moment(date.value).subtract(1, 'month').format('YYYY-MM-DD');
}

watch(date, async () => {
  await getMonthData();
});

let monthDateHours = reactive({ value: {} });
let monthData = ref([]);
let allData = ref([]);
const monthTasks = computed(() => {
  const tasks = allData.value.filter((move) => {
    return (
      moment(move.date).format('YYYY-MM') ==
      moment(date.value).format('YYYY-MM')
    );
  });
  //sort by date, newest first
  tasks.sort((a, b) => {
    return new Date(b.date) - new Date(a.date);
  });
  return tasks;
});

async function getMonthData() {
  // Fetch data from database
  allData.value = (
    await getById('cache', GROUP.toLowerCase().replaceAll(' ', '-') + '_moves')
  ).data._rawValue.value;

  const monthDates = [];
  monthData = [];
  monthDateHours.value = {};

  const startDate = moment(date.value).startOf('month'); // Get the start date of the month
  const endDate = moment(date.value).endOf('month'); // Get the end date of the month

  let currentDate = startDate.clone();

  while (currentDate.isSameOrBefore(endDate)) {
    monthDates.push(currentDate.format('YYYY-MM-DD'));
    currentDate.add(1, 'day');
  }
  monthData = monthDates.map((date) => {
    return allData.value.filter((move) => move.date === date);
  });
  monthData.map((date, index) => {
    monthDateHours.value[monthDates[index]] = +(
      cvTime(sumTime(date.map((move) => move.duration))) * 24
    ).toFixed(2);
  });
}

onMounted(async () => {
  await nextTick();
  await getMonthData(today);
});

const average = computed(() => {
  const today = new Date();
  const currentDate = today.toISOString().split('T')[0];
  const pastHours = Object.entries(monthDateHours.value)
    .filter(([date]) => date < currentDate) // Filter out future and today's dates
    .map(([_, hour]) => hour); // Get the hour values
  if (pastHours.length > 0) {
    const sum = pastHours.reduce((total, hour) => total + hour, 0);
    return (sum / pastHours.length).toFixed(2);
  }
  return 0;
});

const total = computed(() => {
  return Object.values(monthDateHours.value)
    .reduce((acc, hour) => acc + hour, 0)
    .toFixed(1);
});
</script>
<style>
.today .date {
  @apply font-bold bg-blue-800 text-white;
}
.today .column {
  @apply font-bold bg-yellow-300;
}
</style>
