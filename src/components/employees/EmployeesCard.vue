<script>
export default {
    name: 'Employee Card',
    props: {
        index: Number,
        therapist: Object
    }
}
</script>

<template>
    <div class="employee-card mb-5 row align-items-center" :class="index % 2 === 0 ? 'flex-row-reverse' : 'flex-row'">
        <div class="col-5 d-flex flex-column align-items-center justify-content-center">
            <div class="col-12 d-flex flex-column justify-content-between align-items-center">
                <h4 class="mb-3">{{ therapist.user.username }}</h4>
            </div>
            <img class="employee-pic border rounded-circle img-fluid mb-3" :src="therapist.user.profilePic"
                :alt="therapist.user.username">
            <div class="col-12 d-flex flex-column justify-content-between align-items-center">
                <p class="vote mb-1">Vote: <i v-for="i in 5" :key="i" class="fa-star"
                        :class="i <= therapist.reviewAverage ? 'fa-solid' : 'fa-regular'"></i></p>
                <p class="vote mb-0">({{ therapist.reviews.length ??
                    '0'
                }} Reviews)</p>
            </div>
        </div>
        <div class="col-7 mb-2">
            <p class="description mb-0" :class="index % 2 === 0 ? 'text-end' : 'text-start'">
                {{ therapist.description }}
            </p>
        </div>
    </div>
</template>

<style scoped lang="scss">
.employee-card {

    .employee-pic {
        height: 120px;
        width: 120px;
        object-fit: cover;
        object-position: top;
    }

    h4 {
        font-size: 20px;
    }

    .description,
    .vote {

        font-size: 12px;
    }

}
</style>