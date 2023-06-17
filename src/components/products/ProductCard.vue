<script>
export default {
    name: 'Product Card'
}
</script>

<template>
    <div class="product-card row mb-5">
        <div class="col-4 d-flex flex-column justify-content-center align-items-center text-center">
            <h4>PRODUCT NAME</h4>
            <img class="img-fluid"
                src="https://it.thelibraryoffragrance.eu/wp-content/uploads/2020/06/cotton-candy-body-oil.jpg"
                alt="product">
        </div>
        <div class="col-8">
            <p class="description">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Reprehenderit vitae suscipit
                esse. Repellat odio
                nemo fugit esse accusamus doloribus officiis perferendis ad! Enim minima dolore deserunt animi cupiditate
                fuga praesentium.</p>
            <div class="buy d-flex justify-content-around align-items-center">
                <p class="price mb-0"><b class="me-1">Price:</b> ₱200</p>
                <button class="btn btn-primary">Buy</button>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
h4 {
    font-size: 16px;
}

.description,
.price,
button {

    font-size: 12px;
}
</style>