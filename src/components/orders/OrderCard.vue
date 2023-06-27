<script>
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/'

export default {
    name: "Order Card",
    props: {
        purchaseOrder: Object,
        forAdmin: Boolean,
    },
    emits: ['order-handled'],
    computed: {
        pickupDate() {
            const pickupDate = new Date(this.purchaseOrder.dateOfPickUp);
            let month = pickupDate.getMonth() + 1;
            if (month < 10) month = "0" + month;
            let day = pickupDate.getDate();
            if (day < 10) day = "0" + day;
            const year = pickupDate.getFullYear();
            return month + "/" + day + "/" + year;
        },
        price() {
            return this.purchaseOrder.quantity * this.purchaseOrder.product.price
        },
        status() {
            if (!this.purchaseOrder.accepted && !this.purchaseOrder.rejected) return "pending";
            if (this.purchaseOrder.delivered) return "delivered";
            if (this.purchaseOrder.accepted) return "accepted";
            if (this.purchaseOrder.rejected) return "rejected";
        },
        notHandled() {
            return !this.purchaseOrder.accepted && !this.purchaseOrder.rejected && !this.purchaseOrder.delivered && !this.purchaseOrder.canceled
        }
    },
    methods: {
        handleOrder(action) {
            axios.patch(baseApiUrl + 'purchaseorders/' + this.purchaseOrder.id + '/' + action)
                .then(() => this.$emit('order-handled', this.forAdmin ? 'admin' : 'user'))
                .catch(e => console.log(e))
        }
    }
}
</script>

<template>
    <div class="col-12">
        <div class="order-card rounded-3 d-flex py-3 mb-3">
            <div class="col-4 d-flex align-items-center">
                <img class="product-pic" :src="purchaseOrder.product.imageUrl" :alt="purchaseOrder.product.name">
            </div>
            <div class="col-8 flex-wrap d-flex justify-content-between align-items-center">
                <div class="col-12 text-center mb-3">
                    <h5 class="mb-1">{{ purchaseOrder.product.name }}</h5>
                    <p class="mb-0">Q.ty: {{ purchaseOrder.quantity }}</p>
                </div>
                <div v-if="!forAdmin" class="col-6 text-center mb-4">
                    <h6>Status:</h6>
                    <div class="status">
                        <div v-if="status === 'pending'" class="pending">
                            <i class="fa-solid fa-circle-exclamation"></i>
                            <p class="mb-0">Pending</p>
                        </div>
                        <div v-if="status === 'accepted'" class="accepted">
                            <i class="fa-solid fa-circle-check"></i>
                            <p class="mb-0">Accepted</p>
                        </div>
                        <div v-if="status === 'delivered'" class="accepted">
                            <i class="fa-solid fa-circle-check"></i>
                            <p class="mb-0">Delivered</p>
                        </div>
                        <div v-if="status === 'rejected'" class="rejected">
                            <i class="fa-solid fa-circle-xmark"></i>
                            <p class="mb-0">Rejected</p>
                        </div>
                    </div>
                </div>
                <div v-if="forAdmin" class="col-6 text-center mb-4">
                    <h6>User:</h6>
                    <p class="mb-0">{{ purchaseOrder.user.username }}</p>
                </div>
                <div v-if="!purchaseOrder.delivery" class="col-6 text-center mb-4">
                    <h6>Pickup date:</h6>
                    <p class="mb-0">{{ pickupDate }}</p>
                </div>
                <div class="col-12 text-center">
                    <p>Price: ₱{{ price }}</p>
                </div>
                <div v-if="!forAdmin && !purchaseOrder.delivered && !purchaseOrder.rejected"
                    class="col-12 buttons d-flex justify-content-center">
                    <button class="btn btn-danger" @click="handleOrder('cancel')">Cancel Order</button>
                </div>
                <div v-if="forAdmin" class="col-12 admin-buttons d-flex flex-column align-items-center">
                    <h6 v-if="notHandled">
                        Accept order?</h6>
                    <div class="text-center" v-if="notHandled">
                        <button class="btn btn-success me-2" @click="handleOrder('accept')"><i
                                class="fa-solid fa-check"></i></button>
                        <button class="btn btn-danger" @click="handleOrder('reject')"><i
                                class="fa-solid fa-xmark"></i></button>
                    </div>
                    <div v-if="purchaseOrder.accepted && !purchaseOrder.delivered && !purchaseOrder.canceled"
                        class="d-flex align-items-center justify-content-center">
                        <p class="mb-0 accepted me-2">ACCEPTED</p>
                        <button class="btn btn-success" @click="handleOrder('delivered')"><i
                                class="fa-solid fa-truck"></i></button>
                    </div>
                    <p v-if="purchaseOrder.rejected && !purchaseOrder.canceled" class="mb-0 rejected">DECLINED</p>
                    <p v-if="purchaseOrder.delivered" class="mb-0 accepted">DELIVERED</p>
                    <p v-if="purchaseOrder.canceled" class="mb-0 rejected">CANCELED</p>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
.order-card {
    background-color: white;

    .product-pic {
        height: 100px;
        width: 100px;
        object-fit: contain;
        object-position: center;
    }

    p {
        font-size: 12px;

        &.accepted,
        &.rejected {
            font-size: 16px;
            font-weight: bold;
        }
    }

    h5 {
        font-size: 18px;
    }

    h6 {
        font-size: 14px;
    }

    .pending {
        color: rgb(255, 193, 7);
    }

    .accepted {
        color: rgb(25, 135, 84);
    }

    .rejected {
        color: rgb(220, 53, 69)
    }

    .admin-buttons {

        button {
            width: 30px;
            height: 30px;
            padding: 0;
            font-size: 12px;
        }
    }

}
</style>