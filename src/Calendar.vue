<template>
  <div class="year">
    <div class="month" v-for="month in yearData">
      <div class="title">{{month.title}}</div>
      <div class="week">
        <div class="day" v-for="d in weekDays">
          <span class="week-day">{{d}}</span>
        </div>
      </div>
      <div class="week" v-for="week in month.weeks">
        <div class="day" v-for="day in 7" :class="{[`week-day-${day}`]: true, 'work-day': week[day] && week[day].isWorkDay, 'off-day': week[day] && !week[day].isWorkDay}">
          <span v-if="week[day]">{{week[day].date.getDate()}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
  .title {
    margin: 0.25em;
    font-weight: bold;
  }

  .week-day-7 {
    color: red;
  }

  .week {
    display: flex;
  }

  .week-day {
    color: white;
    font-size: 10pt;
  }

  .day {
    padding: 0.25em;
    flex-grow: 1;
    flex-basis: 0;
    color: black;
    border: solid black 0.5px;
    background-color: rgb(23, 55, 93);
    text-align: center;
  }

  .work-day {
    background-color: rgb(247, 150, 70);
  }

  .off-day {
    background-color: rgb(0, 176, 80);
  }

  .year {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
  }

  .month {
    margin: 0.25em;
    flex-basis: 0;
    flex-grow: 1;
  }
</style>

<script>
  import moment from 'moment'

  require('moment/locale/en-gb.js');

  var currentYearSettings = {
    worksDays: 2,
    offDays: 2,
    shift: 3,
  };

  function isWorkingDay(settings, year, i){
    let currentYear = new Date().getFullYear();
    let yearDiff = year - currentYear;
    let daysInDiff = 0;
    for(let y = 0; y < yearDiff; y++) {
      for(let m = 1; m < 13; m++){
        daysInDiff += moment(currentYear + y + "-" + m, "YYYY-M").daysInMonth();
      }
    }
    
    let index = daysInDiff + i + settings.shift;
    var rem = index % (settings.worksDays + settings.offDays);
    return rem < settings.worksDays;
  }

  export default {
    props: {
      year: {  // год на который строится календарь
        type: Number,
        default: (new Date()).getFullYear()
      },
    },
    data() {
      return {}
    },
    computed: {
      weekDays () {
        let days = [];
        for(let i = 1; i<=7;++i) {
          days.push(moment().isoWeekday(i).format("dd"))
        }
        return days;
      },
      yearData() {
        let i = 0;
        let data = [];
        let isWeekStartsWithPrevYear = false;
        for (let m = 0; m < 12; ++m) {
          let day = moment({year: this.year, month: m, day: 1}); // формируем дату на первый день каждого месяца
          let daysInMonth = day.daysInMonth(); // количество дней в месяце
          let month = { // готовим объект месяца
            title: day.format("MMMM"),
            weeks: {},
          };

          // итерируем по количеству дней в месяце
          for (let d = 0; d < daysInMonth; ++d) {
            let week = day.isoWeek();
            let weekYear = day.isoWeekYear();

            if (weekYear < this.year) {
              isWeekStartsWithPrevYear = true;
              week = 1;
            } else if (isWeekStartsWithPrevYear) {
              week += 1;
            }

            // если неделя еще не присутствует в месяце, то добавляем ее
            if (!month.weeks.hasOwnProperty(week)) {
              month.weeks[week] = {}
            }
            // добавляем день, у weekday() нумерация с нуля,
            // поэтому добавляю единицу, можно и не добавлять,
            // но так будет удобнее
            month.weeks[week][day.weekday() + 1] = {
              date: day.toDate(),
              isWorkDay: isWorkingDay(currentYearSettings, this.year, i),
            };

            // итерируем день на единицу, moment мутирует исходное значение
            day.add(1, 'd');
            ++i;
          }

          // добавлям данные по месяцу в год
          data.push(month);
        }
        return data
      }
    }
  }
</script>
