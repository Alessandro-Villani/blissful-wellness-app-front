<script>
export default {
    name: 'Booking Calendar',
    props: {
        massage: Object,
        therapists: Array,
        user: Object,
    },
    data() {
        return {
            today: new Date().getDate(),
            month: new Date().getMonth(),
            currentMonth: new Date().getMonth(),
            year: new Date().getFullYear(),
            currentYear: new Date().getFullYear(),
            currentHour: new Date().getHours(),
            weekDays: ['S', 'M', 'T', 'W', 'T', 'F', 'S'],
            monthsNames: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
            selectedDay: null,
            selectedMonth: null,
            selectedYear: null,
            settedHomeService: false,
            calendarReduced: false,
            therapistsReduced: false,
            timeSelectionReduced: false,
            homeServiceReduced: false,
            selectedTherapist: null,
            startTime: null,
            endTime: null,
            startHour: null,
            endHour: null,
            homeService: false,
            address: '',
        }
    },
    computed: {
        booking() {
            return {
                userId: this.user.id,
                massageId: this.massage.id,
                therapistId: this.selectedTherapist.id,
                date: new Date(this.selectedYear, this.selectedMonth, this.selectedDay),
                startHour: this.startHour,
                endHour: this.endHour,
                totalHours: this.endTime - this.startTime,
                homeService: this.homeService,
                address: this.homeService ? this.address : null,
                price: (this.endTime - this.startTime) * this.massage.pricePerHour,
            }
        },
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
        selectedTime() {
            if (this.startHour && this.endHour) return 'From ' + this.startHour + ':00 to ' + this.endHour + ':00';
            return null;
        },
        homeServiceDisplay() {
            if (this.homeService && this.address && this.settedHomeService) return 'Home service at: ' + this.address;
            if (!this.homeService && this.settedHomeService) return 'No home service';
            return 'Do you require home service?';
        }
    },
    methods: {
        selectDay(day) {
            if (!(day < this.today && this.isCurrentMonthAndYear) && !(this.year < this.currentYear)) {
                this.selectedTherapist = null;
                this.therapistsReduced = false;
                this.timeSelectionReduced = false;
                this.startTime = null;
                this.endTime = null;
                this.startHour = null;
                this.endHour = null;
                this.settedHomeService = false;
                this.homeService = false;
                this.homeServiceReduced = false;
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
            this.timeSelectionReduced = false;
            this.startTime = null;
            this.endTime = null;
            this.startHour = null;
            this.endHour = null;
            this.settedHomeService = false;
            this.homeService = false;
            this.homeServiceReduced = false;
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
            this.settedHomeService = false;
            this.homeService = false;
            this.homeServiceReduced = false;
            const iEnd = i + 1;
            if (this.startTime === i && this.endTime === iEnd) {
                this.startTime = null;
                this.endTime = null;
                return
            }
            if (!this.startTime || this.startTime > i) this.startTime = i
            else if (this.startTime === i) this.startTime = i + 1
            if (!this.endTime || this.endTime < iEnd) this.endTime = iEnd
            else if (this.endTime === iEnd) this.endTime = iEnd - 1
        },
        hourClass(i) {
            const iEnd = i + 1;
            if (this.selectedDay === this.today && this.isCurrentMonthAndYear && i + 12 <= this.currentHour + 1) return "d-none"
            if (i >= this.startTime && iEnd <= this.endTime) return "selected"
        },
        confirmTime() {
            this.startHour = this.startTime + 12 > 24 ? this.startTime + 12 - 24 : this.startTime + 12;
            this.endHour = this.endTime + 12 > 24 ? this.endTime + 12 - 24 : this.endTime + 12;
            this.timeSelectionReduced = true;
        },
        rescheduleTime() {
            this.startHour = null;
            this.endHour = null;
            this.timeSelectionReduced = false;
        },
        setHomeService(answer) {
            if (answer === 'yes') this.homeService = true;
            else {
                this.homeService = false;
                this.settedHomeService = true;
                this.address = '';
            }
        },
        openHomeSevice() {
            this.homeService = false;
            this.settedHomeService = false;
            this.homeServiceReduced = false;
        },
        confirmAddress() {
            if (this.address) {
                this.homeService = true;
                this.settedHomeService = true;
                this.homeServiceReduced = true;
            }
        }
    }
}
</script>

<template>
    <h1 class="text-center mb-5">Book {{ massage.name }} massage </h1>
    <!-- CALENDAR -->
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
    <!-- THERAPIST SELECT -->
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
    <!-- HOUR SELECT -->
    <div v-if="selectedDay && selectedTherapist">
        <h4 class="text-center">{{ selectedTime ? selectedTime : 'Select Time' }}</h4>
        <div class="hours-section text-center" :class="timeSelectionReduced ? 'reduced' : ''">
            <div class="hours mb-3">
                <div class="hour d-flex" v-for="i in 13" :key="i" @click="selectTime(i)" :class="hourClass(i)">
                    <div class="col-3 p-2 text-center">
                        <p class="mb-0">{{ i + 12 > 24 ? i + 12 - 24 : i + 12 }}:00</p>
                        <p class="mb-0">to</p>
                        <p class="mb-0">{{ (i + 1 + 12 > 24 ? i + 12 - 23 : i + 1 + 12) }}:00</p>
                    </div>
                    <div class="col-9 p-2 d-flex justify-content-center align-items-center">
                        <h6 class="mb-0">AVAILABLE</h6>
                    </div>
                </div>
            </div>
            <button class="btn btn-success" @click="confirmTime()">Confirm</button>
        </div>
        <div class="text-center mb-3" v-if="timeSelectionReduced">
            <i class="fa-solid fa-chevron-down" @click="rescheduleTime()"></i>
        </div>
    </div>
    <!-- HOME SERVICE -->
    <div class="home-service" v-if="selectedDay && selectedTherapist && selectedTime">
        <h4 class="text-center">{{ homeServiceDisplay }}</h4>
        <div class="place-select" :class="settedHomeService ? 'reduced' : ''">
            <div v-if="!this.homeService" class="buttons d-flex justify-content-center">
                <button class="btn btn-primary me-2" @click="setHomeService('yes')">Yes</button>
                <button class="btn btn-secondary" @click="setHomeService('no')">No</button>
            </div>
            <div v-else class="d-flex flex-column align-items-center">
                <label for="address">Insert your address</label>
                <input class="mb-3" type="text" name="address" id="address" v-model="address">
                <div class="text-center">
                    <button class="btn btn-success me-2" @click="confirmAddress()">Confirm</button>
                    <button class="btn btn-secondary" @click="openHomeSevice()">Cancel</button>
                </div>
            </div>
        </div>
        <div class="text-center mb-3" v-if="settedHomeService">
            <i class="fa-solid fa-chevron-down" @click="openHomeSevice()"></i>
        </div>
    </div>
    <!-- CONFIRMATION -->
    <div class="text-center mt-5" v-if="selectedDay && selectedTherapist && selectedTime && settedHomeService">
        <h4 class="mb-3">Price: ₱{{ booking.price }}</h4>
        <button class="btn btn-success">Confirm Booking</button>
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

    select,
    input {
        border: none;
    }

    input {
        width: 20%;
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

.hours-section {
    max-height: 1000px;
    overflow: hidden;
    transition: all 2s;

    &.reduced {
        max-height: 0;
        transition: all 0.8s;
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

        &.disabled {
            background-color: gray;
        }

        .col-3 {
            border-right: 1px solid lightblue;
        }

        p {
            font-size: 12px;
        }
    }


}

.place-select {
    max-height: 1000px;
    overflow: hidden;
    transition: all 2s;

    &.reduced {
        max-height: 0;
        transition: all 0.8s;
    }

    .buttons button {
        width: 52px;
    }
}
</style>