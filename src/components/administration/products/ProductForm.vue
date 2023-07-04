<script>
export default {
    name: 'Product Form',
    data() {
        return {
            product: {
                isEdit: this.formType === 2,
                id: this.formType === 2 ? this.existingProduct.id : null,
                product: {
                    name: this.formType === 2 ? this.existingProduct.name : '',
                    description: this.formType === 2 ? this.existingProduct.description : '',
                    price: this.formType === 2 ? this.existingProduct.price : null,
                    stockQuantity: this.formType === 2 ? this.existingProduct.stockQuantity : null,
                },
                imageUrl: null,
            }
        }
    },
    methods: {
        handleFileChange(event) {
            const file = event.target.files[0];
            this.product.imageUrl = file;
        },
    },
    props: {
        formType: Number,
        existingProduct: Object
    },
    emits: ['product', 'back']
}

</script>

<template>
    <form @submit.prevent="$emit('product', product)">
        <div class="card p-5 d-flex flex-column align-items-center">
            <h2 class="text-center mb-3"><span v-if="formType === 1">ADD
                </span><span v-else>EDIT</span> PRODUCT</h2>
            <img v-if="product.product.imageUrl" :src="product.product.imageUrl" :alt="product.product.name"
                class="product-pic img-fluid mb-3">
            <label for="name">Name</label>
            <input class="mb-3" type="text" name="name" id="name" v-model="product.product.name">
            <label for="imageUrl">Image Url</label>
            <input class="mb-3" type="file" name="imageUrl" id="imageUrl" @change="handleFileChange">
            <label for="price">Price</label>
            <input class="mb-3" type="number" min="0" name="price" id="price" v-model="product.product.price">
            <label for="stockQuantity">Quantity in Stock</label>
            <input class="mb-3" type="number" min="0" name="stockQuantity" id="stockQuantity"
                v-model="product.product.stockQuantity">
            <label for="description">Description</label>
            <textarea class="mb-3" name="description" id="description" rows="10"
                v-model="product.product.description"></textarea>
            <div class="buttons d-flex col-12 justify-content-center">
                <button type="button" class="btn btn-secondary back" @click="$emit('back')"><i
                        class="fa-solid fa-arrow-left"></i></button>
                <button class="btn btn-success">Confirm</button>
            </div>
        </div>
    </form>
</template>

<style scoped lang="scss">
.product-pic {
    height: 100px;
    width: 100px;
}

.buttons {
    position: relative;

    .btn.back {
        position: absolute;
        left: 0;
    }
}
</style>