<template>
  <div id="app" class="calendar">
    <div class="header row flex-middle">
      <div className="col col-start">
        <div className="icon">上个月</div>
      </div>
      <div className="col col-center">
        <span>{{ currentMonth.format('YYYY年 MMMM') }}</span>
      </div>
      <div className="col col-end">
        <div className="icon">下个月</div>
      </div>
    </div>
    <div class="days row">
      <div
        v-for="(item, index) in [6, 0, 1, 2, 3, 4, 5]"
        class="col col-center"
        :key="index"
      >
        {{ now.weekday(item).format('ddd') }}
      </div>
    </div>
    <div class="body">
      <div v-for="(row, index) in arrayOfDays[0]" class="row" key="{index}">
        <div
          v-for="d in row.dates"
          class="col cell"
          :class="{
            disabled: !d.isCurrentMonth,
          }"
        >
          <span class="number">{{ d.day }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import dayjs from 'dayjs';
import locale from 'dayjs/locale/zh-cn';
import objectPlugin from 'dayjs/plugin/toObject';
import weekdayPlugin from 'dayjs/plugin/weekday';
import isTodayPlugin from 'dayjs/plugin/isToday';

dayjs.extend(weekdayPlugin);
dayjs.extend(objectPlugin);
dayjs.extend(isTodayPlugin);

export default {
  name: 'App',
  components: {
    HelloWorld,
  },
  data() {
    return {
      now: null,
      currentMonth: null,
      arrayOfDays: [],
    };
  },
  created() {
    this.now = dayjs().locale({
      ...locale,
    });
    this.currentMonth = this.now;
  },
  mounted() {
    console.log(this.arrayOfDays);
  },
  methods: {
    getAllDays() {
      let currentDate = this.currentMonth.startOf('month').weekday(0);
      const nextMonth = this.currentMonth.add(1, 'month').month();

      let allDates = [];
      let weekDates = [];

      let weekCounter = 1;

      while (currentDate.weekday(0).toObject().months !== nextMonth) {
        const formated = this.formateDateObject(currentDate);

        weekDates.push(formated);

        if (weekCounter === 7) {
          allDates.push({ dates: weekDates });
          weekDates = [];
          weekCounter = 0;
        }

        weekCounter++;
        currentDate = currentDate.add(1, 'day');
      }

      this.arrayOfDays.push(allDates);
    },
    formateDateObject(date) {
      const clonedObject = { ...date.toObject() };

      const formatedObject = {
        day: clonedObject.date,
        month: clonedObject.months,
        year: clonedObject.years,
        isCurrentMonth: clonedObject.months === this.currentMonth.month(),
        isCurrentDay: date.isToday(),
      };

      return formatedObject;
    },
  },
  watch: {
    currentMonth(val) {
      if (val) {
        this.getAllDays();
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
header {
  display: block;
  width: 100%;
  padding: 1.75em 0;
  border-bottom: 1px solid var(--border-color);
  background: var(--neutral-color);
}

header #logo {
  font-size: 175%;
  text-align: center;
  color: var(--main-color);
  line-height: 1;
}

header #logo .icon {
  padding-right: 0.25em;
}

main {
  display: block;
  margin: 0 auto;
  margin-top: 5em;
  max-width: 50em;
}

/* GRID */

.row {
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  width: 100%;
}

.row-middle {
  align-items: center;
}

.col {
  flex-grow: 1;
  flex-basis: 0;
  max-width: 100%;
}

.col-start {
  justify-content: flex-start;
  text-align: left;
}

.col-center {
  justify-content: center;
  text-align: center;
}

.col-end {
  justify-content: flex-end;
  text-align: right;
}

/* Calendar */

.calendar {
  display: block;
  position: relative;
  width: 100%;
  background: var(--neutral-color);
  border: 1px solid var(--border-color);
}

.calendar .header {
  text-transform: uppercase;
  font-weight: 700;
  font-size: 115%;
  padding: 1.5em 0;
  border-bottom: 1px solid var(--border-color);
}

.calendar .header .icon {
  cursor: pointer;
  transition: 0.15s ease-out;
}

.calendar .header .icon:hover {
  transform: scale(1.75);
  transition: 0.25s ease-out;
  color: var(--main-color);
}

.calendar .header .icon:first-of-type {
  margin-left: 1em;
}

.calendar .header .icon:last-of-type {
  margin-right: 1em;
}

.calendar .days {
  text-transform: uppercase;
  font-weight: 400;
  color: var(--text-color-light);
  font-size: 70%;
  padding: 0.75em 0;
  border-bottom: 1px solid var(--border-color);
}

.calendar .body .cell {
  position: relative;
  height: 5em;
  border-right: 1px solid var(--border-color);
  overflow: hidden;
  cursor: pointer;
  background: var(--neutral-color);
  transition: 0.25s ease-out;
}

.calendar .body .cell:hover {
  background: var(--bg-color);
  transition: 0.5s ease-out;
}

.calendar .body .selected {
  border-left: 10px solid transparent;
  border-image: linear-gradient(45deg, #1a8fff 0%, #53cbf1 40%);
  border-image-slice: 1;
}

.calendar .body .row {
  border-bottom: 1px solid var(--border-color);
}

.calendar .body .row:last-child {
  border-bottom: none;
}

.calendar .body .cell:last-child {
  border-right: none;
}

.calendar .body .cell .number {
  position: absolute;
  font-size: 82.5%;
  line-height: 1;
  top: 0.75em;
  right: 0.75em;
  font-weight: 700;
}

.calendar .body .disabled {
  color: red;
  pointer-events: none;
}

.calendar .body .cell .bg {
  font-weight: 700;
  line-height: 1;
  color: var(--main-color);
  opacity: 0;
  font-size: 8em;
  position: absolute;
  top: -0.2em;
  right: -0.05em;
  transition: 0.25s ease-out;
  letter-spacing: -0.07em;
}

.calendar .body .cell:hover .bg,
.calendar .body .selected .bg {
  opacity: 0.05;
  transition: 0.5s ease-in;
}

.calendar .body .col {
  flex-grow: 0;
  flex-basis: calc(100% / 7);
  width: calc(100% / 7);
}
</style>
