<script>
import axios from 'axios';

import ProductQuantityPatchForm from './ProductQuantityPatchForm.vue';
const baseApiUrl = 'http://localhost:8080/api/v1/';

export default {
    name: "Products Management",
    data() {
        return {
            products: [],
            searchProduct: "",
        };
    },
    methods: {
        fetchProducts() {
            const params = {
                params: {
                    name: this.searchProduct
                }
            }
            axios.get(baseApiUrl + "products", params)
                .then(res => this.products = res.data)
                .catch(e => console.log(e));
        },
        deleteProduct(id) {
            axios.delete(baseApiUrl + "products/delete/" + id)
                .then(() => this.fetchProducts())
                .catch(e => console.log(e))
        },
        patchProductQuantity(product) {
            axios.patch(baseApiUrl + "products/" + product.id + "/quantity", product)
                .then(() => this.fetchProducts())
                .catch(e => console.log(e))
        },
        imageUrl(product) {
            return 'http://localhost:8080/' + product.imageUrl;
        }
    },
    emits: ["back", "add-product", "edit-product"],
    mounted() {
        this.fetchProducts();
    },
    components: { ProductQuantityPatchForm }
}
</script>

<template>
    <div class="title d-flex flex-column align-items-center">
        <h2 class="mb-3">Manage Products</h2>
        <form @submit.prevent="fetchProducts" class="input-group mb-3">
            <input type="text" class="form-control" placeholder="Search product..." v-model="searchProduct">
            <button class="btn btn-primary"><i class="fa-solid fa-magnifying-glass"></i></button>
        </form>
        <div class="head-buttons d-flex justify-content-center w-100">
            <button class="btn btn-secondary back" @click="$emit('back', 0)"><i class="fa-solid fa-arrow-left"></i></button>
            <button class="btn btn-success mb-3" @click="$emit('add-product')">Add product</button>
        </div>
    </div>
    <div v-for="product in products" :key="product.id" class="row mb-1">
        <div class="card py-3 d-flex flex-row align-items-center justify-content-between">
            <div class="d-flex align-items-center">
                <img :src="imageUrl(product)" :alt="product.name" class="product-pic img-fluid col-4">
                <p class="mb-0 col-6">{{ product.name }}</p>
                <ProductQuantityPatchForm :patchProduct="product" @patch-quantity="patchProductQuantity" />
            </div>
            <div class="d-flex align-items-center buttons">
                <button class="btn btn-warning me-2" @click="$emit('edit-product', product.id)"><i
                        class="fa-regular fa-pen-to-square"></i></button>
                <button class="btn btn-danger" @click="deleteProduct(product.id)"><i
                        class="fa-regular fa-trash-can"></i></button>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
.product-pic {
    height: 60px;
    width: 60px;
}

.head-buttons {
    position: relative;

    .btn.back {
        position: absolute;
        left: 0;
    }
}
</style>