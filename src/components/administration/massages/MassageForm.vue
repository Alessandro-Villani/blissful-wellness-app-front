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
                    pricePerHour: this.isEdit ? this.existingMassage.pricePerHour : 0,
                    color: this.isEdit ? this.existingMassage.color : "#000000",
                    therapists: this.isEdit ? this.existingMassage.therapists.map(therapist => therapist.id) : [],
                },
                imageUrl: null,
            },
            loadedImageUrl: null,
        }

    },
    methods: {
        handleFileChange(event) {
            const file = event.target.files[0];
            this.massage.imageUrl = file;
            this.loadedImageUrl = this.massage.imageUrl ? URL.createObjectURL(this.massage.imageUrl) : null;
        },
        isChecked(therapist) {
            return this.massage.massage.therapists.includes(therapist.id);
        },
    },
    computed: {
        imageUrl() {
            let imageUrl = null;
            if (this.loadedImageUrl) imageUrl = this.loadedImageUrl;
            if (this.isEdit && !this.loadedImageUrl) imageUrl = 'http://localhost:8080/' + this.existingMassage.imageUrl;
            return imageUrl;
        },
        existingMassageTherapists() {
            if (this.isEdit) {
                let existingMassageTherapists = this.therapists.filter(therapist => {
                    const massageIds = therapist.massages.map(massage => massage.id);
                    return massageIds.includes(this.existingMassage.id);
                })
                existingMassageTherapists = existingMassageTherapists.map(therapist => therapist.id);
                this.massage.massage.therapists = existingMassageTherapists;
                return existingMassageTherapists;
            }
            return []
        }
    },
    emits: ['massage', 'back'],
    props: {
        isEdit: Boolean,
        existingMassage: Object,
        therapists: Array,
    },
}
</script>

<template>
    <form @submit.prevent="$emit('massage', massage)">
        <div class="card p-5 d-flex flex-column align-items-center">
            <h2><span v-if="!isEdit">INSERT NEW</span><span v-if="isEdit">EDIT</span> MASSAGE</h2>
            <img v-if="imageUrl" :src="imageUrl" alt="preview" class="massage-pic rounded-circle img-fluid mb-3">
            <label for="name">Name</label>
            <input class="mb-3" type="text" name="name" id="name" v-model="massage.massage.name">
            <label for="imageUrl">Image Url</label>
            <input class="mb-3" type="file" name="imageUrl" id="imageUrl" @change="handleFileChange">
            <label for="description">Description</label>
            <textarea class="mb-3" name="description" id="description" rows="10"
                v-model="massage.massage.description"></textarea>
            <label for="price">Price</label>
            <input class="mb-4" type="number" name="price" id="price" v-model="massage.massage.pricePerHour">
            <label for="color">Color</label>
            <input class="mb-4" type="color" name="color" id="color" v-model="massage.massage.color">
            <div class="therapists row border rounded-2 p-3 mb-4">
                <div class="col-6" v-for="therapist in therapists">
                    <input class="me-2" type="checkbox" :value="therapist.id" :id="'therapist' + therapist.id"
                        :checked="isChecked(therapist)" v-model="massage.massage.therapists"><label
                        :for="'therapist' + therapist.id">{{
                            therapist.therapistName }}</label>
                </div>
            </div>
            <div class="buttons d-flex col-12 justify-content-center">
                <button type="button" class="btn btn-secondary back" @click="$emit('back')"><i
                        class="fa-solid fa-arrow-left"></i></button>
                <button class="btn btn-success">Confirm</button>
            </div>
        </div>
    </form>
</template>

<style scoped lang="scss">
.massage-pic {
    height: 120px;
    width: 120px;
    object-fit: cover;
    object-position: center;
}

.buttons {
    position: relative;

    .btn.back {
        position: absolute;
        left: 0;
    }
}
</style>