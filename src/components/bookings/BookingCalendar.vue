<script>
export default {
    name: 'Booking Calendar',
    props: {
        massage: Object,
        therapists: Array,
    },
    data() {
        return {
            today: new Date().getDate(),
            month: new Date().getMonth(),
            currentMonth: new Date().getMonth(),
            year: new Date().getFullYear(),
            currentYear: new Date().getFullYear(),
            weekDays: ['S', 'M', 'T', 'W', 'T', 'F', 'S'],
            monthsNames: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
            selectedDay: null,
            selectedMonth: null,
            selectedYear: null,
            calendarReduced: false,
            therapistsReduced: false,
            selectedTherapist: null,
            startTime: null,
            endTime: null,
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
            return this.monthsNames[this.month];
        },
        monthDays() {
            return Math.floor(((this.endDate - this.startDate) / 86400000) + 1);
        },
        isCurrentMonthAndYear() {
            const currentMonth = new Date().getMonth();
            const currentYear = new Date().getFullYear();
            return currentMonth === this.month && currentYear === this.year;
        },
        remainingMonths() {
            let months = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11];
            if (this.year === this.currentYear) {
                months = months.slice(this.currentMonth, months.length);
            }
            return months;
        },
        ableTherapists() {
            const ableTherapists = this.therapists.filter(therapist => therapist.massages.map(massage => massage.id).includes(this.massage.id));
            return ableTherapists;
        },
        formattedSelectedDate() {
            const day = this.selectedDay < 10 ? '0' + this.selectedDay : this.selectedDay;
            const month = this.selectedMonth + 1 < 10 ? '0' + (this.selectedMonth + 1) : this.selectedMonth + 1;
            return day + '/' + month + '/' + this.selectedYear;
        },
        startHour() {
            if (this.startTime) return this.startTime + 5 > 12 ? this.startTime + 5 - 12 : this.startTime + 5;
            return null;
        },
        endHour() {
            if (this.endTime) return this.endTime + 5 > 12 ? this.endTime + 5 - 12 : this.endTime + 5;
            return null;
        }
    },
    methods: {
        selectDay(day) {
            if (!(day < this.today && this.isCurrentMonthAndYear) && !(this.year < this.currentYear)) {
                this.selectedTherapist = null;
                if (day === this.selectedDay && this.selectedMonth === this.month && this.selectedYear === this.year) {
                    this.selectedDay = null;
                    this.selectedMonth = null;
                    this.selectedYear = null;
                    return;
                }
                this.selectedDay = day;
                this.selectedMonth = this.month;
                this.selectedYear = this.year;
                this.calendarReduced = true;
            };
        },
        selectTherapist(therapist) {
            this.selectedTherapist = therapist;
            this.therapistsReduced = true;
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
        },
        dayClasses(day) {
            let classes = '';
            if (day < this.today && this.isCurrentMonthAndYear || this.year < this.currentYear) classes += 'past';
            if (day === this.selectedDay && this.month === this.selectedMonth && this.year === this.selectedYear) classes += ' selected';
            return classes;
        },
        dayOffset(day) {
            if (day === 1) {
                const thisDay = new Date(this.year, this.month, day);
                const thisDayOfTheWeek = thisDay.getDay();
                const offset = 'margin-left: calc(100%/7*' + thisDayOfTheWeek + ')';
                return offset;
            }
            else return '';
        },
        selectTime(i) {
            const iEnd = i + 1;
            if (!this.startTime || this.startTime > i) this.startTime = i
            if (!this.endTime || this.endTime < iEnd) this.endTime = iEnd
        },
        hourClass(i) {
            const iEnd = i + 1;
            if (i >= this.startTime && iEnd <= this.endTime) return "selected"
        }
    }
}
</script>

<template>
    <h1 class="text-center mb-5">Book {{ massage.name }} massage </h1>
    <h4 class="text-center">{{ selectedDay ? formattedSelectedDate : 'Select Day' }}</h4>
    <div class="calendar-container" :class="calendarReduced ? 'reduced mb-0' : ''">
        <div class="row mb-3 align-items-center">
            <div class="col-2 text-center"><i v-if="!isCurrentMonthAndYear" class="fa-solid fa-caret-left"
                    @click="goToMonth('prev')"></i></div>
            <div class="col-8 text-center month-year py-1">
                <select class="text-center me-2" name="month" id="month" v-model="month">
                    <option v-for="m in remainingMonths" :value="m" :selected="m === month">{{ monthsNames[m] }}</option>
                </select>
                <input type="number" :min="currentYear" v-model="year">
            </div>
            <div class="col-2 text-center"><i class="fa-solid fa-caret-right" @click="goToMonth('next')"></i></div>
        </div>
        <div class="calendar d-flex flex-wrap p-2 rounded-2 mb-3" :class="calendarReduced ? 'reduced mb-0' : ''">
            <div v-for="(weekDay, i) in weekDays" class="weekday text-center mb-1" :class="i === 0 ? 'holiday' : ''">{{
                weekDay
            }}</div>
            <div v-for="(day, i) in monthDays" class="day text-center" :class="dayClasses(day)" :style="dayOffset(day)"
                @click="selectDay(day)">{{
                    day }}</div>
        </div>
    </div>
    <div class="text-center mb-3" v-if="calendarReduced">
        <i class="fa-solid fa-chevron-down" @click="calendarReduced = false"></i>
    </div>
    <div v-if="selectedDay">
        <h4 class="text-center" v-if="!selectedTherapist">Select Therapist</h4>
        <div class="selected-therapist d-flex align-items-center justify-content-center"
            :class="therapistsReduced ? '' : 'open'" v-else>
            <img class="selected-therapist-pic me-3" :src="selectedTherapist.user.profilePic"
                :alt="selectedTherapist.therapistName">
            <p class="text-center mb-0"><b>{{ selectedTherapist.therapistName }}</b></p>
        </div>
        <div class="therapists row justify-content-center" :class="therapistsReduced ? 'reduced mb-0' : ''">
            <div class="col-3" v-for="therapist in ableTherapists">
                <div class="therapist" @click="selectTherapist(therapist)"
                    :class="therapist === selectedTherapist ? 'selected' : ''">
                    <img class="therapist-pic mb-1" :src="therapist.user.profilePic" :alt="therapist.therapistName">
                    <p class="text-center mb-0">{{ therapist.therapistName }}</p>
                </div>
            </div>
        </div>
    </div>
    <div class="text-center mb-3" v-if="therapistsReduced">
        <i class="fa-solid fa-chevron-down" @click="therapistsReduced = false"></i>
    </div>
    <div v-if="selectedDay && selectedTherapist">
        <h4 class="text-center">Select Time</h4>
        <div class="hours mb-3">
            <div class="hour d-flex" v-for="i in 8" :key="i" @click="selectTime(i)" :class="hourClass(i)">
                <div class="col-3 p-2 text-center">
                    <p class="mb-0">{{ i + 5 > 12 ? i + 5 - 12 : i + 5 }}:00</p>
                    <p class="mb-0">to</p>
                    <p class="mb-0">{{ (i + 5 + 1 > 12 ? i + 5 - 11 : i + 5 + 1) }}:00</p>
                </div>
                <div class="col-9 p-2 d-flex justify-content-center align-items-center">
                    <h6 class="mb-0">AVAILABLE</h6>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
.weekday {
    flex-basis: calc(100% / 7);
    font-weight: bold;

    &.holiday {
        color: red;
    }

}

.calendar {
    background-color: white;
}

.calendar-container {
    max-height: 1000px;
    overflow: hidden;
    transition: max-height 2s;

    &.reduced {
        max-height: 1px;
        padding-top: 2px;
        transition: max-height 1s, padding 0.2s 0.5s;
    }

}

.day {
    flex-basis: calc(100% / 7);
    border: 0.5px solid black;
    background-color: white;

    &.selected {
        background-color: green;
    }

    &.past {
        background-color: gray;

    }

}



.month-year {
    background-color: white;
    border-radius: 15px;
}

select,
input {
    border: none;
}

input {
    width: 20%;
}

.therapists {
    max-height: 1000px;
    overflow: hidden;
    transition: max-height 2s, padding 0.2s;

    &.reduced {
        max-height: 1px;
        padding-top: 2px;
        transition: max-height 1s, padding 0.2s 0.5s;
    }
}

.therapist {

    filter: grayscale(1);
    transform: scale(0.9);
    transition: all 0.5s;

    .therapist-pic {
        height: 80px;
        width: 80px;
        object-fit: cover;
        object-position: top;
        border-radius: 50%;
    }

    &.selected {
        filter: grayscale(0);
        transform: scale(1);
    }

}

.selected-therapist {
    margin-bottom: 0.25rem;
    transition: all 0.8s 0.5s;

    &.open {
        margin-bottom: 1rem;
        transition: all 0.5s;
    }

    .selected-therapist-pic {
        height: 80px;
        width: 80px;
        object-fit: cover;
        object-position: top;
        border-radius: 50%;
    }
}

.hours {
    max-height: 200px;
    background-color: white;
    overflow-y: auto;
    border: 0.5px solid lightblue;

    .hour {

        border: 0.5px solid lightblue;

        &.selected {
            background-color: rgb(62, 14, 50);
            color: white;
        }

        .col-3 {
            border-right: 1px solid lightblue;
        }

        p {
            font-size: 12px;
        }
    }


}
</style>