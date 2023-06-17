<script>
export default {
    name: 'Massage Form',
    data() {

        return {
            massage: {
                isEdit: this.isEdit,
                id: this.isEdit ? this.existingMassage.id : null,
                massage: {
                    name: this.isEdit ? this.existingMassage.name : '',
                    description: this.isEdit ? this.existingMassage.description : '',
                    pricePerHour: this.isEdit ? this.existingMassage.pricePerHour : 0
                }
            }
        }

    },
    emits: ['massage', 'back'],
    props: {
        isEdit: Boolean,
        existingMassage: Object
    }
}
</script>

<template>
    <form @submit.prevent="$emit('massage', massage)">
        <div class="card p-5 d-flex flex-column align-items-center">
            <h2><span v-if="!isEdit">INSERT NEW</span><span v-if="isEdit">EDIT</span> MASSAGE</h2>
            <label for="name">Name</label>
            <input class="mb-3" type="text" name="name" id="name" v-model="massage.massage.name">
            <label for="description">Description</label>
            <textarea class="mb-3" name="description" id="description" rows="10"
                v-model="massage.massage.description"></textarea>
            <label for="price">Price</label>
            <input class="mb-4" type="number" name="price" id="price" v-model="massage.massage.pricePerHour">
            <div class="buttons d-flex col-12 justify-content-center">
                <button type="button" class="btn btn-secondary back" @click="$emit('back')"><i
                        class="fa-solid fa-arrow-left"></i></button>
                <button class="btn btn-success">Confirm</button>
            </div>
        </div>
    </form>
</template>

<style scoped lang="scss">
.buttons {
    position: relative;

    .btn.back {
        position: absolute;
        left: 0;
    }
}
</style>