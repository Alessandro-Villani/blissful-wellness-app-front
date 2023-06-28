<script>
export default {
    name: 'Booking Calendar',
    props: {
        massage: Object,
    },
    data() {
        return {
            today: new Date().getDate(),
            month: new Date().getMonth(),
            year: new Date().getFullYear(),
        }
    },
    computed: {
        startDate() {
            return new Date(this.year, this.month, 1)
        },
        endDate() {
            return new Date(this.year, this.month + 1, 0)
        },
        monthName() {
            const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
            return months[this.month];
        },
        monthDays() {
            return Math.floor(((this.endDate - this.startDate) / 86400000) + 1);
        },
        isCurrentMonthAndYear() {
            const currentMonth = new Date().getMonth();
            const currentYear = new Date().getFullYear();
            return currentMonth === this.month && currentYear === this.year;
        }
    },
    methods: {
        selectDay(day) {
            if (!(day < this.today && this.isCurrentMonthAndYear)) console.log('book');
        },
        goToMonth(direction) {
            if (direction === 'next') {
                if (this.month === 11) {
                    this.month = 0;
                    this.year++;
                }
                else this.month++;
            }
            if (direction === 'prev') {
                if (this.month === 0) {
                    this.month = 11;
                    this.year--;
                }
                else this.month--;
            }
        }
    }
}
</script>

<template>
    <h1 class="text-center mb-5">Book {{ massage.name }} massage </h1>
    <div class="row mb-3 align-items-center">
        <div class="col-2 text-center"><i v-if="!isCurrentMonthAndYear" class="fa-solid fa-caret-left"
                @click="goToMonth('prev')"></i></div>
        <div class="col-8 text-center month-year py-1">{{ monthName }}, {{ year }}</div>
        <div class="col-2 text-center"><i class="fa-solid fa-caret-right" @click="goToMonth('next')"></i></div>
    </div>
    <div class="d-flex flex-wrap">
        <div v-for="day in monthDays" class="day text-center" :class="day < today && isCurrentMonthAndYear ? 'past' : ''"
            @click="selectDay(day)">{{
                day }}</div>
    </div>
</template>

<style scoped lang="scss">
.day {
    flex-basis: calc(100% / 7);
    border: 1px solid black;
    background-color: white;
}

.past {
    background-color: gray;
}

.month-year {
    background-color: white;
    border-radius: 15px;
}
</style>