<script>
import tt from '@tomtom-international/web-sdk-maps';
import '@tomtom-international/web-sdk-maps/dist/maps.css';
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/'

export default {
    name: 'Booking Card',
    data() {
        return {
            rejectionReason: {
                rejectionReason: '',
            },
            rejectionMessageOpen: false,
            review: {
                grade: 1,
                review: '',
                therapist: this.booking.therapist.id,
                author: this.booking.user.id,
                massageName: this.booking.massage.name,
                duration: this.booking.totalHours,
                date: null
            },
            chat: {
                userId: this.booking.user.id,
                therapistId: this.booking.therapist.id,
            },
            showDetails: false,
        }
    },
    props: {
        booking: Object,
        userRole: String,
    },
    emits: ['booking-handled', 'review-store', 'chat'],
    computed: {
        bookingDate() {
            const date = new Date(this.booking.date);
            let day = date.getDate();
            day = day < 10 ? '0' + day : day;
            let month = date.getMonth() + 1;
            month = month < 10 ? '0' + month : month;
            const year = date.getFullYear();
            return day + '/' + month + '/' + year
        },
        status() {
            if (!this.booking.accepted && !this.booking.rejected && !this.booking.completed) return 'pending';
            if (this.booking.rejected) return 'rejected';
            if (this.booking.completed) return 'completed';
            if (this.booking.accepted) return 'accepted';
        }
    },
    methods: {
        imageUrl(user) {
            return 'http://localhost:8080/' + user.profilePic;
        },
        toggleDetails() {
            this.showDetails = !this.showDetails;
        },
        acceptBooking() {
            axios.patch(baseApiUrl + 'bookings/' + this.booking.id + '/accept')
                .then(() => this.$emit('booking-handled'))
                .catch(e => console.log(e));
        },
        completeBooking() {
            axios.patch(baseApiUrl + 'bookings/' + this.booking.id + '/complete')
                .then(() => this.$emit('booking-handled'))
                .catch(e => console.log(e));
        },
        rejectBooking() {
            axios.patch(baseApiUrl + 'bookings/' + this.booking.id + '/reject', this.rejectionReason)
                .then(() => {
                    this.$emit('booking-handled')
                    this.rejectionMessageOpen = false;
                })
                .catch(e => console.log(e));
        },
        sendReview() {
            axios.post(baseApiUrl + 'reviews/store', this.review)
                .then(() => {
                    axios.patch(baseApiUrl + 'bookings/' + this.booking.id + '/reviewed')
                        .then(() => {
                            console.log('reviewed');
                            this.$emit('review-store');
                        })
                        .catch(e => console.log(e))

                })
                .catch(e => console.log(e))
        },
        openChat() {
            axios.post(baseApiUrl + 'chats/store', this.chat)
                .then(res => {
                    const chatId = res.data.id;
                    this.$emit('chat', chatId);
                })
                .catch(e => console.log(e))
        },
    },
    mounted() {
        if (this.booking.homeService) {
            tt.setProductInfo('VueTomTomMaps', '1.0');
            const map = tt.map({
                key: 'lCdijgMp1lmgVifAWwN8K9Jrfa9XcFzm',
                container: 'map' + this.booking.id, // L'id dell'elemento HTML in cui verrà visualizzata la mappa
                center: [this.booking.longitude || 0, this.booking.latitude || 0], // Le coordinate di centro per la mappa
                zoom: 15, // Il livello di zoom iniziale della mappa
            })
            const marker = new tt.Marker({
                color: '#3E0E32'
            }).setLngLat([this.booking.longitude, this.booking.latitude]).addTo(map);
        }
        this.review.date = this.bookingDate;
    }
}
</script>

<template>
    <div class="card p-5 text-center mb-3" :class="status">
        <small class="booking-n">Booking no. {{ booking.id }}</small>
        <h1 class="mb-3">{{ booking.massage.name }} Massage</h1>
        <p class="mb-1">{{ bookingDate }}</p>
        <small class="mb-3">{{ booking.startHour }}:00 - {{ booking.endHour }}:00 ({{ booking.totalHours }}
            hrs)</small>
        <!-- DETAILS -->
        <div class="details" :class="showDetails ? '' : 'reduced'">
            <div class="text-center" v-if="userRole === 'user' || userRole === 'admin'">
                <p class="mb-1">Therapist</p>
                <img :src="imageUrl(booking.therapist.user)" :alt="booking.therapist.therapistName"
                    class="therapist-pic mb-1">
                <p>{{ booking.therapist.therapistName }}</p>
            </div>
            <div class="text-center" v-if="userRole === 'therapist' || userRole === 'admin'">
                <p class="mb-1">Customer</p>
                <img :src="imageUrl(booking.user)" :alt="booking.user.username" class="therapist-pic mb-1">
                <p>{{ booking.user.username }}</p>
            </div>
            <!-- MAP -->
            <div v-if="booking.homeService" class="map-container d-flex justify-content-center mb-3">
                <div :id="'map' + this.booking.id" class="map"></div>
            </div>
            <p class="mb-3">{{ booking.homeService ? booking.address : 'In Spa' }}</p>
        </div>
        <div class="d-flex align-items-center justify-content-center mb-3" @click="toggleDetails">
            <p class="mb-0 me-2" v-if="!showDetails">Show details</p><i class="fa-solid fa-chevron-down"
                :class="showDetails ? 'rotated' : ''"></i>
        </div>
        <!-- PRICE -->
        <h4 :class="userRole === 'user' || status === 'pending' || status === 'accepted' ? 'mb-3' : 'mb-0'">Price: ₱{{
            booking.price }}</h4>
        <!-- BOOKING STATUS -->
        <p class="mb-0" v-if="userRole === 'user'">Status: <span class="status-text">{{ status }}</span></p>
        <p class="mt-3" v-if="userRole === 'user' && status === 'rejected'">Reason: {{ booking.rejectionReason }}</p>
        <!-- REVIEW -->
        <div class="text-center d-flex flex-column align-items-center mt-5"
            v-if="userRole === 'user' && status === 'completed' && !booking.reviewed">
            <h6 class="mb-3">Rate your experience</h6>
            <p class="mb-3">Rating: <i v-for="  i   in   5  " :key="i" class="fa-star"
                    :class="i <= review.grade ? 'fa-solid' : 'fa-regular'" @click="review.grade = i"></i></p>
            <textarea class="mb-3" name="review" id="review" rows="10" placeholder="Leave a feedback..."
                v-model="review.review"></textarea>
            <button class="btn btn-primary" @click="sendReview">Review</button>
        </div>
        <!-- BOOKING ADMINISTRATION -->
        <div v-if="userRole != 'user'" class="buttons">
            <div class="accept" v-if="status === 'pending'">
                <h6>Accept Booking?</h6>
                <button class="btn btn-success me-2" @click="acceptBooking">Yes</button>
                <button class="btn btn-danger" @click="rejectionMessageOpen = true">No</button>
            </div>
            <div class="complete" v-if="status === 'accepted'">
                <h6>Completed?</h6>
                <button class="btn btn-success rounded-circle" @click="completeBooking"><i
                        class="fa-solid fa-check"></i></button>
            </div>
        </div>
        <!-- CHAT -->
        <div class="text-center mt-3" v-if="status === 'pending' || status === 'accepted'">
            <button class="btn btn-primary" @click="openChat()">{{ userRole === 'therapist' ?
                'Chat Customer' : 'Chat Therapist' }}</button>
        </div>
    </div>
    <!-- REJECTION MESSAGE FORM -->
    <div class="rejection-message overlay d-flex align-items-center justify-content-center" v-if="rejectionMessageOpen">
        <div class="card p-5 text-center">
            <label class="mb-1" for="rejectionReason">Rejection Reason</label>
            <textarea class="mb-3" id="rejectionReason" name="rejectionReason" v-model="rejectionReason.rejectionReason"
                rows="10"></textarea>
            <button class="btn btn-success" @click="rejectBooking">Confirm</button>
            <button class="btn btn-closure" @click="rejectionMessageOpen = false"><i class="fa-solid fa-xmark"></i></button>
        </div>
    </div>
</template>

<style scoped lang="scss">
.card {
    position: relative;

    &.accepted {
        background-color: lightgreen;
    }

    &.rejected {
        background-color: lightcoral;
    }

    &.pending {
        background-color: lightyellow;
    }

    &.completed {
        background-color: lightgray;
    }

    .booking-n {
        position: absolute;
        top: 10px;
        left: 10px;
    }

}

.details {
    max-height: 1000px;
    overflow-y: hidden;
    transition: all 1s;

    &.reduced {
        max-height: 0;
    }

}

.fa-chevron-down {
    transition: all 0.5s;

    &.rotated {
        transform: rotate(180deg);
    }
}

.map {
    height: 200px;
    width: 200px;
    border-radius: 10px;
    box-shadow: 0 0 5px rgba($color: #000000, $alpha: 0.6);
}

.therapist-pic {
    height: 80px;
    width: 80px;
    object-fit: cover;
    object-position: top;
    border-radius: 50%;
}

.status-text {
    text-transform: capitalize;
}

.overlay {
    background-color: rgba($color: #000000, $alpha: 0.5);
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 10001;

    .card {
        position: relative;

        .btn-closure {
            position: absolute;
            top: 5px;
            right: 10px;
            font-size: 18px;

            &:hover {
                color: red;
            }
        }
    }
}
</style>

