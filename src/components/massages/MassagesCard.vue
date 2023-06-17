<script>
export default {
    name: 'Massage Card',
    props: {
        index: Number,
        massage: Object
    }
}
</script>

<template>
    <div class="massage-card mb-5 row align-items-center" :class="index % 2 === 0 ? 'flex-row-reverse' : 'flex-row'">
        <div class="col-5 d-flex flex-column align-items-center justify-content-between">
            <h4>{{ massage.name }}</h4>
            <img class="massage-pic border rounded-circle img-fluid"
                src="https://www.integrativehealthcare.org/mt/wp-content/uploads/2015/03/6-Ways-to-Protect-Your-Hands.jpg"
                alt="massage">
        </div>
        <div :class="index % 2 === 0 ? 'text-end' : 'text-start'" class="col-7">
            <p class="description mb-3">{{ massage.description }}</p>
            <p class="price mb-0"><b>Price/h: </b>₱{{ massage.pricePerHour }}</p>
        </div>
    </div>
</template>

<style scoped lang="scss">
.massage-card {

    .massage-pic {
        height: 120px;
        width: 120px;
        object-fit: cover;
        object-position: top;
    }

    h4 {
        font-size: 20px;
    }

    .description,
    .price {

        font-size: 12px;
    }
}
</style>