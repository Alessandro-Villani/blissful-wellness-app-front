<script>
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/'

export default {
    name: 'Profile Overview',
    data() {
        return {
            bookings: [],
            profilePic: null,
            loadedProfilePicUrl: null,
            therapist: {},
            therapistsStats: [],
        }
    },
    props: {
        user: Object,
        userRole: String,
    },
    emits: ['change-pic'],
    methods: {
        fetchBookings() {
            if (this.userRole === 'user') {
                axios.get(baseApiUrl + 'bookings/user/' + this.user.id)
                    .then(res => this.bookings = res.data)
                    .catch(e => console.log(e))

            }
            if (this.userRole === 'therapist') {
                axios.get(baseApiUrl + 'therapists/user/' + this.user.id)
                    .then(res => {
                        const therapist = res.data
                        axios.get(baseApiUrl + 'bookings/therapist/' + therapist.id)
                            .then(res => this.bookings = res.data)
                            .catch(e => console.log(e))
                    })
                    .catch(e => console.log(e))

            }
            if (this.userRole === 'admin') {
                axios.get(baseApiUrl + 'bookings')
                    .then(res => this.bookings = res.data)
                    .catch(e => console.log(e))

            }
        },
        openFileInput() {
            this.$refs.picInput.click();
        },
        handlePicChange(event) {
            const pic = event.target.files[0];
            this.profilePic = pic;
            this.loadedProfilePicUrl = this.profilePic ? URL.createObjectURL(this.profilePic) : null;
            if (this.profilePic) this.changeProfilePic();
        },
        changeProfilePic() {
            console.log('profile-pic');
            const formData = new FormData();
            formData.append('file', this.profilePic)
            axios.patch(baseApiUrl + 'users/' + this.user.id + '/changeprofilepic', formData, {
                headers: {
                    'Content-Type': 'multipart/form-data'
                }
            })
                .then(res => {
                    const user = res.data;
                    this.$emit('change-pic', user);
                })
        },
        fetchTherapist() {
            axios.get(baseApiUrl + 'therapists/user/' + this.user.id)
                .then(res => this.therapist = res.data)
                .catch(e => console.log(e))
        },
        fetchTherapistStats() {
            axios.get(baseApiUrl + 'therapists/stats')
                .then(res => this.therapistsStats = res.data)
                .catch(e => console.log(e))
        },
        hoursPercentage(therapist) {
            let totalHours = 0;
            this.therapistsStats.forEach(stat => totalHours += stat.massageHours)
            const percentage = (therapist.massageHours / totalHours) * 100;
            return percentage;
        }
    },
    computed: {
        imageUrl() {
            return this.loadedProfilePicUrl ? this.loadedProfilePicUrl : 'http://localhost:8080/' + this.user.profilePic;
        },
        age() {
            return Math.floor((new Date() - new Date(this.user.dateOfBirth)) / 31557600000);
        },
        completedBookingHours() {
            const completedBookings = this.bookings.filter(booking => booking.completed);
            let completedBookingHours = 0;
            completedBookings.forEach(completedBooking => completedBookingHours += completedBooking.totalHours);
            return completedBookingHours;
        },
        hoursToNextCoupon() {
            console.log(this.completedBookingHours - this.user.usedCoupons);
            if (this.completedBookingHours - this.user.usedCoupons > 10) {
                let hoursToNextCoupon = this.completedBookingHours - this.user.usedCoupons;
                while (hoursToNextCoupon > 10) {
                    hoursToNextCoupon -= 10;
                }
                return hoursToNextCoupon;
            }
            return this.completedBookingHours
        },
        lifetimeTherapistEarnings() {
            if (this.userRole === 'therapist') {
                const completedBookings = this.bookings.filter(booking => booking.completed);
                const bookingPrices = completedBookings.map(booking => booking.price);
                const lifetimeEarnings = bookingPrices.reduce((accumulator, currentValue) => {
                    const netValue = currentValue * 0.1;
                    return accumulator + netValue;
                }, 0)
                return lifetimeEarnings;
            }
            return null;
        },
        lifetimeGrossProfit() {
            if (this.userRole === 'admin') {
                const completedBookings = this.bookings.filter(booking => booking.completed);
                const bookingPrices = completedBookings.map(booking => booking.price);
                const lifetimeEarnings = bookingPrices.reduce((accumulator, currentValue) => {
                    return accumulator + currentValue;
                }, 0)
                return lifetimeEarnings;
            }
            return null;
        },
        lifetimeNetIncome() {
            if (this.userRole === 'admin') {
                return this.lifetimeGrossProfit * 0.9;
            }
            return null;
        }
    },
    mounted() {
        this.fetchBookings();
        if (this.userRole === 'therapist') this.fetchTherapist();
        if (this.userRole === 'admin') this.fetchTherapistStats();
    }
}
</script>

<template>
    <div class="profile-data mb-3">
        <div class="image-username flex-row align-items-center card rounded-pill py-3 px-2 mb-3">
            <div class="pic-container text-center col-6">
                <img class="profile-pic" :src="imageUrl" :alt="user.username" @click="openFileInput()">
                <i class="fa-solid fa-repeat"></i>
            </div>
            <div class="col-6 text-center">
                <h2 class="mb-0">{{ user.username }}</h2>
                <h6 :class="userRole" v-if="userRole === 'therapist' || userRole === 'admin'">{{ userRole }}</h6>
            </div>
        </div>
        <div class="personal-data flex-row p-2 card rounded-pill">
            <div class="col-4 text-center">
                <small>Name:</small>
                <p class="mb-0"><b>{{ user.firstName }}</b></p>
            </div>
            <div class="col-4 text-center">
                <small>Surname:</small>
                <p class="mb-0"><b>{{ user.lastName }}</b></p>
            </div>
            <div class="col-4 text-center">
                <small>Age:</small>
                <p class="mb-0"><b>{{ age }}</b></p>
            </div>
        </div>
    </div>
    <!-- USER -->
    <div class="user-stats card p-3 d-flex" v-if="userRole === 'user'">
        <div class="col-12">
            <p class="text-center mb-3">Lifetime massage hours: <b>{{ completedBookingHours }}</b></p>
            <p class="text-center mb-4">Lifetime used coupons: <b>{{ user.usedCoupons }}</b></p>
            <div class="stamp-container rounded p-3 mb-4">
                <p class="mb-2 text-center">Massage hours to next coupon:</p>
                <div class="stamp-massage d-flex justify-content-between">
                    <div class="stamp d-flex align-item-center justify-content-center"
                        :class="i <= hoursToNextCoupon ? 'completed' : 'incompleted'" v-for="i in 10" :key="i">
                        <i class="fa-solid fa-hand-sparkles"></i>
                    </div>
                </div>
            </div>
            <div class="coupon text-center d-flex flex-column align-items-center">
                <p class="mb-1">Coupon for 1 hour of <b>FREE</b> massage</p>
                <i class="fa-solid fa-ticket fa-3x"></i>
                <p class="mb-0"><b>{{ user.couponQuantity }}</b></p>
            </div>
        </div>
    </div>
    <!-- THERAPIST -->
    <div class="user-stats card p-3 d-flex text-center" v-if="userRole === 'therapist'">
        <p class="mb-1">Rating</p>
        <div class="d-flex justify-content-center align-items-center mb-3">
            <i class="fa-solid fa-star me-1"></i>
            <span class="mt-1"><b>{{ therapist.reviewAverage }}</b></span>
        </div>
        <p class="text-center mb-3">Lifetime massage hours: <b>{{ completedBookingHours }}</b></p>
        <p class="text-center mb-3">Lifetime earnings: <b>₱{{ lifetimeTherapistEarnings }}</b></p>
    </div>
    <!-- ADMIN -->
    <div class="user-stats card p-3 d-flex text-center" v-if="userRole === 'admin'">
        <p class="text-center mb-3">Lifetime gross profit: <b>₱{{ lifetimeGrossProfit }}</b></p>
        <p class="text-center mb-3">Lifetime net income: <b>₱{{ lifetimeNetIncome }}</b></p>
        <p class="text-center mb-3">Total completed massage hours: <b>{{ completedBookingHours }}</b></p>
        <div class="stats p-2 rounded-2">
            <p class="text-center mb-1"><b>Therapists performance</b></p>
            <div class="row">
                <div class="col d-flex flex-column" v-for="therapist in therapistsStats">
                    <div class="stats-col-container d-flex flex-column justify-content-end px-2">
                        <div class="stats-col rounded" :style="'height:' + hoursPercentage(therapist) + '% ;'"></div>
                        <small class="massage-hours">{{ therapist.massageHours }}h</small>
                    </div>
                    <small>{{ therapist.therapistName }}</small>
                    <div>
                        <i class="fa-solid fa-star me-1"></i>
                        <span class="mt-1">{{ therapist.reviewsAverage }}</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <input type="file" class="d-none" ref="picInput" @change="handlePicChange">
</template>

<style scoped lang="scss">
b {
    color: rgb(253, 1, 165);
}

h2 {
    color: rgb(253, 1, 165);
}

.profile-data {

    .pic-container {
        position: relative;

        i {
            color: rgb(253, 1, 165);
            position: absolute;
            bottom: 15px;
            right: 15px;
        }
    }

    .profile-pic {
        width: 150px;
        height: 150px;
        border-radius: 50%;
        object-fit: cover;
        object-position: center;
        overflow-y: hidden;
    }

    h6 {
        text-transform: capitalize;

        &.therapist {
            color: orange;
        }

        &.admin {
            color: red;
        }
    }

}

.user-stats {

    .stamp-container {
        background-color: rgb(62, 14, 50);
        color: white;

        .stamp {

            height: 30px;
            width: 30px;
            border: 1px solid;
            border-radius: 50%;
            padding: 5px;

            &.incompleted {
                color: rgba($color: white, $alpha: 0.5);
            }

            &.completed {
                color: rgba($color: rgb(253, 1, 165), $alpha: 1.0);
            }

        }

    }

    .coupon {
        .fa-ticket {
            color: rgb(62, 14, 50);
        }
    }


}

.stats {
    background-color: rgb(62, 14, 50);
    color: white;

    b {
        color: white;
    }

    .stats-col-container {
        height: 100px;
        position: relative;

        .stats-col {
            background-color: rgb(253, 1, 165);
        }

        .massage-hours {
            font-weight: bold;
            font-size: 10px;
            position: absolute;
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
        }

    }
}
</style>

