<script>
export default {
    name: 'Product Card',
    props: {
        product: Object,
        userRole: String,
    },
    emits: ['order-form'],
    methods: {
        imageUrl(product) {
            return 'http://localhost:8080/' + product.imageUrl;
        }
    }
}
</script>

<template>
    <div class="product-card row mb-5 position-relative">
        <div class="col-4 d-flex flex-column justify-content-between align-items-center text-center">
            <h4>{{ product.name.toUpperCase() }}</h4>
            <img class="product-pic" :src="imageUrl(product)" :alt="product.name">
        </div>
        <div class="col-8 col-4 d-flex flex-column justify-content-between">
            <p class="description">{{ product.description }}</p>
            <div class="buy d-flex justify-content-around align-items-center">
                <p class="price mb-0"><b class="me-1">Price:</b> ₱{{ product.price }}</p>
                <button class="btn btn-primary" :disabled="product.stockQuantity < 1" @click="$emit('order-form', product)"
                    v-if="userRole === 'user'">Order</button>
            </div>
        </div>
        <div class="out-of-stock p-2" v-if="product.stockQuantity < 1"><b>OUT OF STOCK</b></div>
    </div>
</template>

<style scoped lang="scss">
.product-pic {
    height: 100px;
    width: 100px;
    border-radius: 30% 70% 68% 32% / 30% 55% 45% 70%;
    object-fit: cover;
    object-position: center;
    box-shadow: 3px 5px 10px rgba($color: #000000, $alpha: 0.4);
}

h4 {
    font-size: 16px;
}

.description,
.price,
button {

    font-size: 12px;
}

.out-of-stock {
    background-color: red;
    opacity: 0.8;
    text-align: center;
    position: absolute;
    width: 90%;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);

}
</style>