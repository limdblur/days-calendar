<template>
  <div class="calendar-body">
    <div class="weekdays">
        <div class="weekday" v-for="weekday in weekdays">
          <div>{{weekday}}</div>
        </div>
      </ul>
    </div>

    <div class="days">
      <div class="day" v-for="day in days">
        <span class="disabled" v-if="day.disabled">{{day.number}}</span>
        <span v-else :class="day.class" v-on:click="renderContent(day)">{{day.number}}</span>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';

function renderCalendar(year, month, renderDays){
  let days = [];
  const monthStart = new moment().set({year, month, 'date': 1}).startOf('day');
  const lastSunday = new moment().set({year, month, 'date': 1}).day('Sunday').startOf('day');
  const additonDays = monthStart.diff(lastSunday, 'days') -1;

  if(additonDays !== -1){
    days.push({ number: lastSunday.date(), date: new moment(lastSunday), class:[], disabled: true });
    for(let i = additonDays; i !== 0; i--){
      const date = new moment(lastSunday.add(1, 'days'));
      const day = {number:date.date(), date, class:[], disabled: true };
      days.push(day);
    }
  }

  const monthLength = monthStart.endOf('month').date();

  for(let i = 1; i <= monthLength; i++){
    const date = new moment().set({year, month, 'date': i}).startOf('day');
    const day = {number: i, date, class:[]};
    days.push(day);
  }

  if(renderDays){
    days = renderDays(days);
  }

  return days;
}

export default {
  props: {
    'year': Number,
    'month': Number,
    'weekdays': Array,
    'renderDays': Function,
    'renderContent': Function,
  },
  watch: {
    year: function(val){
      this.days = renderCalendar(this.year, this.month, this.renderDays);
    },
    month: function(val){
      this.days = renderCalendar(this.year, this.month, this.renderDays);
    }
  },
  data: function(){
    return {
      'days': renderCalendar(this.year, this.month, this.renderDays)
    };
  }
}
</script>
