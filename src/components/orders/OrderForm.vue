<script>
import axios from 'axios';

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
                latitude: null,
                longitude: null,
                quantity: 1
            },
            openSuggestions: false,
            searchedAddresses: []
        }
    },
    props: {
        userId: Number,
        product: Object,
    },
    emits: ['order-form-close', 'emit-order'],
    methods: {
        deliveryChange() {
            if (this.purchaseOrder.delivery) this.purchaseOrder.dateOfPickup = null
            else {
                this.openSuggestions = false;
                this.searchedAddresses = [];
                this.purchaseOrder.address = '';
                this.purchaseOrder.latitude = null;
                this.purchaseOrder.longitude = null;
            }
        },
        imageUrl(product) {
            return 'http://localhost:8080/' + product.imageUrl;
        },
        searchAddress() {
            this.openSuggestions = true;
            axios.get("https://api.tomtom.com/search/2/search/" + this.purchaseOrder.address + ".json?key=lCdijgMp1lmgVifAWwN8K9Jrfa9XcFzm")
                .then(res => this.searchedAddresses = res.data.results)
                .catch(e => console.log(e))

        },
        selectSuggestion(address) {
            this.purchaseOrder.address = address.address.freeformAddress;
            this.purchaseOrder.latitude = address.position.lat;
            this.purchaseOrder.longitude = address.position.lon;
            this.openSuggestions = false;
        },
        emitOrder() {
            if (!this.openSuggestions) {
                this.$emit('emit-order', this.purchaseOrder)
            }
        }
    }
}
</script>

<template>
    <div class="overlay container d-flex align-items-center justify-content-center">
        <form @submit.prevent="emitOrder" class="card flex-row flex-wrap align-items-center p-5">
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
            <div class="address col-6 d-flex flex-column text-center p-1 mb-5">
                <label class="mb-2" for="address">Delivery Address</label>
                <input type="text" name="address" id="address" :disabled="!purchaseOrder.delivery"
                    v-model="purchaseOrder.address" @keyup="searchAddress">
                <div class="suggestions mx-1" v-if="openSuggestions">
                    <div class="suggestion" v-for="address in searchedAddresses" @click="selectSuggestion(address)">{{
                        address.address.freeformAddress }}</div>
                </div>
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

        .address {
            position: relative;
            z-index: 2;

            .suggestions {
                background-color: white;
                position: absolute;
                top: 66px;
                left: 0;
                right: 0;
                max-height: 60px;
                overflow-y: auto;
                z-index: 1;
                border: 1px solid black;
                border-top: 0;

                .suggestion {
                    border-bottom: 1px solid lightblue;
                    padding: 2px 5px;
                    font-size: 10px;
                }
            }

        }
    }
}
</style>