<script>
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/';

export default {
    name: 'Massage Management',
    props: {
        massages: Array,
    },
    emits: ['back', 'add-massage', 'edit-massage', 'refresh'],
    methods: {
        deleteMasage(id) {
            console.log(id);
            axios.delete(baseApiUrl + 'massages/delete/' + id)
                .then(() => this.$emit('refresh'))
                .catch(e => console.log(e))
        }
    }
}
</script>

<template>
    <div class="title d-flex flex-column align-items-center">
        <h2 class="mb-3">Manage Massages</h2>
        <div class="head-buttons d-flex justify-content-center w-100">
            <button class="btn btn-secondary back" @click="$emit('back', 0)"><i class="fa-solid fa-arrow-left"></i></button>
            <button class="btn btn-success mb-3" @click="$emit('add-massage')">Add massage</button>
        </div>
    </div>
    <div v-for="massage in massages" :key="massage.id" class="row mb-1">
        <div class="card py-3 d-flex flex-row align-items-center justify-content-between">
            <p class="mb-0">{{ massage.name }}</p>
            <div class="d-flex align-items-center buttons">
                <button class="btn btn-warning me-2" @click="$emit('edit-massage', massage.id)"><i
                        class="fa-regular fa-pen-to-square"></i></button>
                <button class="btn btn-danger" @click="deleteMasage(massage.id)"><i
                        class="fa-regular fa-trash-can"></i></button>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
.head-buttons {
    position: relative;

    .btn.back {
        position: absolute;
        left: 0;
    }
}
</style>