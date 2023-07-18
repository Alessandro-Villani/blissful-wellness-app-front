<script>
export default {
    name: 'Massage Card',
    props: {
        index: Number,
        massage: Object,
        userRole: String,
    },
    computed: {
        backgroundColor() {
            return "background-color:" + this.massage.color;
        },
        imageUrl() {
            return 'http://localhost:8080/' + this.massage.imageUrl;
        },
    },
    emits: ['book']
}
</script>

<template>
    <div class="massage-card mb-5 p-3 row align-items-center" :class="index % 2 === 0 ? 'flex-row-reverse' : 'flex-row'"
        :style="backgroundColor">
        <div class="col-5 d-flex flex-column align-items-center justify-content-between mb-2">
            <h4>{{ massage.name }}</h4>
            <img class="massage-pic rounded-circle img-fluid" :src="imageUrl" alt="massage">
        </div>
        <div :class="index % 2 === 0 ? 'text-end' : 'text-start'" class="col-7 mb-2">
            <p class="description mb-3">{{ massage.description }}</p>
            <p class="price text-center mb-0"><b>Price/h: </b>₱{{ massage.pricePerHour }}</p>
        </div>
        <div class="col-12 text-center">
            <button class="btn btn-primary" @click="$emit('book', massage)" v-if="userRole === 'user'">Book</button>
        </div>
    </div>
</template>

<style scoped lang="scss">
.massage-card {
    border-radius: 44% 56% 35% 65% / 31% 62% 38% 69%;
    box-shadow: 0 10px 10px rgba($color: #000000, $alpha: 0.5);

    &.flex-row-reverse {
        border-radius: 54% 46% 56% 44% / 65% 31% 69% 35%;

        .massage-pic {
            box-shadow: 5px 5px 5px rgba($color: #000000, $alpha: 0.5);
        }

        .col-7 {
            box-shadow: -15px -5px 10px rgba($color: #000000, $alpha: 0.5);
        }
    }

    .massage-pic {
        height: 120px;
        width: 120px;
        object-fit: cover;
        object-position: center;
        box-shadow: -5px 5px 5px rgba($color: #000000, $alpha: 0.5);
    }

    h4 {
        font-size: 20px;
    }

    .description,
    .price,
    button {
        font-size: 12px;
    }

    .col-7 {
        border-radius: 49% 51% 52% 48% / 31% 31% 69% 69%;
        background-color: rgb(205, 212, 250);
        box-shadow: 15px -5px 10px rgba($color: #000000, $alpha: 0.5);
        padding: 15px;
    }
}
</style>