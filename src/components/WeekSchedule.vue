<template>
    <div class="week">
        <span v-if="weekInfo.isUpper" class="week-text text">Верхняя неделя</span>
        <span v-else class="week-text text">Нижняя неделя</span>
        <span class="week-text text">
            {{ startDateName }} - {{ endDateName }}
        </span>
        <div class="days">
            <DaySchedule v-for="day in days" :dayInfo="day" :key="day" />
        </div>
    </div>
</template>

<script>
import DaySchedule from './DaySchedule.vue';
import { computed } from 'vue'

export default {
    components: {
        DaySchedule
    },
    props: {
        weekInfo: Object,
        subjects: {type: Array, default() {return []}},
        daysOff: {type: Array, default() {return []}}
    },
    setup(props) {

        props.weekInfo.startDate.setHours(0, 0, 0, 0);
        props.weekInfo.endDate.setHours(0, 0, 0, 0);

        const days = computed(() => {
            let daysInfo = [];
            for (let i = 0; i < 7; i++) {

                let day = new Date(props.weekInfo.startDate);
                day.setDate(day.getDate() + i);

                let skip = props.daysOff.some(x =>
                    x.toLocaleDateString("ru-Ru", { month: '2-digit', day: '2-digit', year: '2-digit'}) 
                    === day.toLocaleDateString("ru-Ru", { month: '2-digit', day: '2-digit', year: '2-digit'}));

                if (skip)
                    if (day.getDate() == props.weekInfo.endDate.getDate()) break;
                    else continue;
                let dayInfo = {};

                dayInfo.subjects = props.subjects.filter((x) =>
                    x.isUpperWeek == props.weekInfo.isUpper
                    && x.startWeek <= props.weekInfo.Number
                    && x.endWeek >= props.weekInfo.Number
                    && x.dayOfWeek == day.getDay());

                dayInfo.date = day;

                daysInfo.push(dayInfo);
                if (day.getDate() == props.weekInfo.endDate.getDate()) break;
            }
            return daysInfo;
        });

        const startDateName = computed(() => props.weekInfo.startDate.toLocaleDateString("ru-Ru", { month: '2-digit', day: '2-digit' }));
        const endDateName = computed(() => props.weekInfo.endDate.toLocaleDateString("ru-Ru", { month: '2-digit', day: '2-digit' }));

        return { days, startDateName, endDateName }
    }

}
</script>

<style scoped>
.days {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
}

.days>* {
    margin: 0px 10px 20px 10px;
}

.week-text {
    font-weight: bolder;
    font-size: 20px;
    padding-bottom: 10px;
    text-transform: capitalize;
    text-align: center;
}

.week {
    display: flex;
    flex-flow: column;
    flex-wrap: wrap;
}
</style>