<template>
  <div style="margin: 10px;">
    <WeekSchedule :weekInfo="thisWeekInfo" :subjects="subjectList" />
    <WeekSchedule :weekInfo="nextWeekInfo" :subjects="subjectList" />
  </div>
</template>

<script>
import WeekSchedule from '@/components/WeekSchedule.vue';
import { ref } from 'vue'

export default {
  components: {
    WeekSchedule
  },
  setup() {

    const firstWeekMonday = new Date(2023, 7, 28);

    const subjectList = ref(null);
    fetch("./subjects.json")
      .then((res) => res.json())
      .then((response) => subjectList.value = response);

    let thisWeekInfo = ref({});
    let nextWeekInfo = ref({});

    let today = new Date();
    let day = today.getDay();
    let diff = today.getDate() - day + 1;
    today.setDate(diff);

    thisWeekInfo.value.startDate = today;
    thisWeekInfo.value.endDate = addDays(thisWeekInfo.value.startDate, 7);
    nextWeekInfo.value.startDate = addDays(thisWeekInfo.value.endDate, 1);
    nextWeekInfo.value.endDate = addDays(nextWeekInfo.value.startDate, 7);

    console.log(nextWeekInfo.value.endDate);

    let timeDifference = thisWeekInfo.value.startDate - firstWeekMonday;
    thisWeekInfo.value.Number = Math.floor(timeDifference / (7 * 24 * 60 * 60 * 1000)) + 1;
    thisWeekInfo.value.isUpper = (thisWeekInfo.value.Number % 2) === 1;

    nextWeekInfo.value.Number = thisWeekInfo.value.Number + 1;
    nextWeekInfo.value.isUpper = !thisWeekInfo.value.isUpper;

    function addDays(date, days) {
      var result = new Date(date);
      result.setDate(result.getDate() + days);
      return result;
    }

    return { thisWeekInfo, nextWeekInfo, subjectList }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: sans-serif;
}

body {
  padding: 5px;
  background-color: rgb(15, 15, 15);
}

.text {
  color: white;
  align-self: center;
}
</style>
