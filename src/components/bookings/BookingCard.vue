<script>
export default {
    name: 'Booking Card',
    props: {
        booking: Object,
        userRole: String,
    },
    computed: {
        bookingDate() {
            const date = new Date(this.booking.date);
            let day = date.getDate();
            day = day < 10 ? '0' + day : day;
            let month = date.getMonth() + 1;
            month = month < 10 ? '0' + month : month;
            const year = date.getFullYear();
            return day + '/' + month + '/' + year
        }
    },
    methods: {
        imageUrl(user) {
            return 'http://localhost:8080/' + user.profilePic;
        }
    }
}
</script>

<template>
    <div class="card p-5 text-center mb-3">
        <h1 class="mb-3">{{ booking.massage.name }} Massage</h1>
        <p class="mb-1">{{ bookingDate }}</p>
        <small class="mb-3">{{ booking.startHour }}:00 - {{ booking.endHour }}:00 ({{ booking.totalHours }}
            hrs)</small>
        <div class="text-center" v-if="userRole === 'user' || userRole === 'admin'">
            <p class="mb-1">Therapist</p>
            <img :src="imageUrl(booking.therapist.user)" :alt="booking.therapist.therapistName" class="therapist-pic mb-1">
            <p>{{ booking.therapist.therapistName }}</p>
        </div>
        <div class="text-center" v-if="userRole === 'therapist' || userRole === 'admin'">
            <p class="mb-1">Customer</p>
            <img :src="imageUrl(booking.user)" :alt="booking.user.username" class="therapist-pic mb-1">
            <p>{{ booking.user.username }}</p>
        </div>
        <p>{{ booking.homeService ? booking.address : 'In Spa' }}</p>

        <h4>Price: ₱{{ booking.price }}</h4>
    </div>
</template>

<style scoped lang="scss">
.therapist-pic {
    height: 80px;
    width: 80px;
    object-fit: cover;
    object-position: top;
    border-radius: 50%;
}
</style>

