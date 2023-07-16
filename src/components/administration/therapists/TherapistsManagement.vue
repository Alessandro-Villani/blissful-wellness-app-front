<script>
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/';

export default {
    name: 'Therapists Management',
    data() {
        return {
            therapists: [],
            searchTherapist: '',
        }
    },
    emits: ['back', 'add-therapist', 'edit-therapist'],
    methods: {
        fetchTherapists() {
            axios.get(baseApiUrl + 'therapists')
                .then(res => this.therapists = res.data)
                .catch(e => console.log(e))
        },
        deleteTherapist(id) {
            axios.delete(baseApiUrl + 'therapists/delete/' + id)
                .then(() => {
                    this.fetchTherapists();
                })
                .catch(e => console.log(e))
        },
        imageUrl(user) {
            return 'http://localhost:8080/' + user.profilePic;
        }
    },
    computed: {
        filteredTherapists() {
            const filteredTherapists = this.therapists.filter(therapist => therapist.therapistName.toLowerCase().includes(this.searchTherapist));
            return filteredTherapists;
        }
    },
    mounted() {

        this.fetchTherapists();

    }
}
</script>

<template>
    <div class="title d-flex flex-column align-items-center">
        <h2 class="mb-3">Manage Therapists</h2>
        <input type="text" class="form-control mb-3" placeholder="Search therapist..." v-model="searchTherapist">
        <div class="head-buttons d-flex justify-content-center w-100">
            <button class="btn btn-secondary back" @click="$emit('back', 0)"><i class="fa-solid fa-arrow-left"></i></button>
            <button class="btn btn-success mb-3" @click="$emit('add-therapist')">Add therapist</button>
        </div>
    </div>
    <div v-for="therapist in filteredTherapists" :key="therapist.id" class="row mb-1">
        <div class="card py-3 d-flex flex-row align-items-center justify-content-between">
            <div class="d-flex align-items-center">
                <img :src="imageUrl(therapist.user)" :alt="therapist.therapistName"
                    class="therapist-pic rounded-circle img-fluid me-3">
                <p class="mb-0">{{ therapist.therapistName }}</p>
            </div>
            <div class="d-flex align-items-center buttons">
                <button class="btn btn-warning me-2" @click="$emit('edit-therapist', therapist)"><i
                        class="fa-regular fa-pen-to-square"></i></button>
                <button class="btn btn-danger" @click="deleteTherapist(therapist.id)"><i
                        class="fa-solid fa-circle-down"></i></button>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
.therapist-pic {
    height: 60px;
    width: 60px;
    object-fit: cover;
    object-position: top;
}

.head-buttons {
    position: relative;

    .btn.back {
        position: absolute;
        left: 0;
    }
}
</style>