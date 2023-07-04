<script>
export default {
    name: 'Purchase Order Form',
    data() {
        return {
            purchaseOrder: {
                userId: this.userId,
                productId: this.product.id,
                delivery: false,
                dateOfPickup: null,
                address: '',
                quantity: 1
            }
        }
    },
    props: {
        userId: Number,
        product: Object,
    },
    emits: ['order-form-close', 'emit-order'],
    methods: {
        deliveryChange() {
            this.purchaseOrder.delivery ? this.purchaseOrder.dateOfPickup = null : this.purchaseOrder.address = '';
        },
        imageUrl(product) {
            return 'http://localhost:8080/' + product.imageUrl;
        }
    }
}
</script>

<template>
    <div class="overlay container d-flex align-items-center justify-content-center">
        <form @submit.prevent="$emit('emit-order', purchaseOrder)" class="card flex-row flex-wrap align-items-center p-5">
            <div class="col-6 mb-5">
                <img class="img-fluid" :src="imageUrl(product)" :alt="product.name">
            </div>
            <div class="col-6 text-center mb-5">
                <h5>{{ product.name }}</h5>
            </div>
            <div class="offset-4 col-4 d-flex flex-column justify-content-center align-items-center mb-4">
                <label class="mb-2" for="quantity">Quantity</label>
                <input class="w-50 text-center mb-2" type="number" id="quantity" name="quantity" min="1"
                    :max="product.stockQuantity" v-model="purchaseOrder.quantity">
                <p class="mb-0">In Stock: {{ product.stockQuantity }}</p>
            </div>
            <div class="col-12 text-center mb-4">
                <label class="me-2" for="delivery">Delivery</label>
                <input type="checkbox" name="delivery" id="delivery" v-model="purchaseOrder.delivery"
                    @change="deliveryChange">
            </div>
            <div class="col-6 d-flex flex-column text-center p-1 mb-5">
                <label class="mb-2" for="dateOfPickup">Date of Pickup</label>
                <input type="date" name="dateOfPickup" id="dateOfPickup" :disabled="purchaseOrder.delivery"
                    v-model="purchaseOrder.dateOfPickup">
            </div>
            <div class="col-6 d-flex flex-column text-center p-1 mb-5">
                <label class="mb-2" for="address">Delivery Address</label>
                <input type="text" name="address" id="address" :disabled="!purchaseOrder.delivery"
                    v-model="purchaseOrder.address">
            </div>
            <div class="col-12 text-center">
                <button class="btn btn-primary">Proceed to Order</button>
            </div>
            <button type="button" class="btn btn-closure" @click="$emit('order-form-close')"><i
                    class="fa-solid fa-xmark"></i></button>
        </form>

    </div>
</template>

<style scoped lang="scss">
.overlay {
    background-color: rgba($color: #000000, $alpha: 0.5);
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 5;

    .card {
        position: relative;

        img {
            width: 120px;
            height: 120px;
            object-fit: contain;
            object-position: center;
        }

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