<script>
export default {
    name: 'Product Quantity Patch Form',
    data() {
        return {
            product: {
                id: this.patchProduct.id,
                stockQuantity: this.patchProduct.stockQuantity,
            }
        }
    },
    props: {
        patchProduct: Object,
    },
    emits: ['patch-quantity']
}
</script>

<template>
    <form @submit.prevent="$emit('patch-quantity', product)" class="col-2 d-flex flex-column text-center">
        <label for="stockQuantity">Stock</label>
        <input class="text-center mb-3" type="number" name="stockQuantity" id="stockQuantity" min="0"
            v-model="product.stockQuantity">
        <button class="btn btn-success btn-ok text-center p-0"><i class="fa-solid fa-check"></i></button>
    </form>
</template>

<style scoped lang="scss">
.btn-ok {
    width: 30px;
    height: 30px;
    line-height: 30px;
    border-radius: 50%;
    align-self: center;

}
</style>