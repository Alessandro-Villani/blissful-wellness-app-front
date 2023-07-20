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
        }
    },
    mounted() {
        this.fetchBookings();
    }
}
</script>

<template>
    <div class="profile-data mb-5">
        <div class="image-username d-flex align-items-center mb-3">
            <div class="pic-container col-5">
                <img class="profile-pic" :src="imageUrl" :alt="user.username" @click="openFileInput()">
                <i class="fa-solid fa-repeat"></i>
            </div>
            <div class="col-7 text-center">
                <h2 class="mb-0">{{ user.username }}</h2>
                <h6 :class="userRole" v-if="userRole === 'therapist' || userRole === 'admin'">{{ userRole }}</h6>
            </div>
        </div>
        <div class="personal-data d-flex">
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
    <div class="user-stats d-flex" v-if="userRole === 'user'">
        <div class="col-12">
            <p class="mb-1 text-center">Lifetime massage hours:</p>
            <p class="text-center mb-3"><b>{{ completedBookingHours }}</b></p>
            <p class="mb-1 text-center">Lifetime used coupons:</p>
            <p class="text-center mb-3"><b>{{ user.usedCoupons }}</b></p>
            <p class="mb-2 text-center">Massage hours to next coupon:</p>
            <div class="stamp-massage d-flex justify-content-between mb-3">
                <div class="stamp d-flex align-item-center justify-content-center"
                    :class="i <= hoursToNextCoupon ? 'completed' : 'incompleted'" v-for="i in 10" :key="i">
                    <i class="fa-solid fa-hand-sparkles"></i>
                </div>
            </div>
            <div class="coupon text-center d-flex flex-column align-items-center">
                <p class="mb-1">Coupon for 1 hour of <b>FREE</b> massage</p>
                <i class="fa-solid fa-ticket fa-3x"></i>
                <p class="mb-0">{{ user.couponQuantity }}</p>
            </div>
        </div>
    </div>
    <input type="file" class="d-none" ref="picInput" @change="handlePicChange">
</template>

<style scoped lang="scss">
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

    .stamp {

        height: 30px;
        width: 30px;
        border: 1px solid;
        border-radius: 50%;
        padding: 5px;

        &.incompleted {
            color: rgba($color: #000000, $alpha: 0.5);
        }

        &.completed {
            color: rgba($color: green, $alpha: 1.0);
        }

    }


}
</style>

