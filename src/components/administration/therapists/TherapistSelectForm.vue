<script>
import axios from 'axios';
import TherapistForm from './TherapistForm.vue';

const baseApiUrl = 'http://localhost:8080/api/v1/';

export default {
    name: 'Therapist Select Form',
    data() {
        return {
            users: [],
            username: '',
            formState: 0,
            selectedUser: {}
        }
    },
    components: { TherapistForm },
    emits: ['back'],
    methods: {
        fetchUsers() {
            const params = {
                params: {
                    username: this.username
                }
            }
            console.log('fetch');
            axios.get(baseApiUrl + 'users', params)
                .then(res => this.users = res.data)
                .catch(e => console.log(e))

        },
        storeTherapist(therapist) {
            axios.post(baseApiUrl + 'therapists/store', therapist)
                .then(() => this.$emit('back'))
                .catch(e => console.log(e))
        },
        openForm(user) {
            this.selectedUser = user;
            this.formState = 1;
        }
    },
    mounted() {
        this.fetchUsers();
    }
}
</script>

<template>
    <div class="user-select" v-if="formState === 0">
        <h2 class="text-center mb-3">Select the User to promote to Therapist</h2>
        <form @submit.prevent="fetchUsers" class="input-group mb-3">
            <input type="text" class="form-control" placeholder="Search user..." v-model="username">
            <button class="btn btn-primary"><i class="fa-solid fa-magnifying-glass"></i></button>
        </form>
        <button type="button" class="btn btn-secondary back mb-3" @click="$emit('back')"><i
                class="fa-solid fa-arrow-left"></i></button>
        <div class="row">
            <div class="col-12 card py-2 flex-row mb-1 justify-content-center" v-for="user in users" :key="user.id"
                @click="openForm(user)">
                <div class="col-2 d-flex align-items-center justify-content-center"><img :src="user.profilePic"
                        :alt="user.username" class="user-pic rounded-circle img-fluid border"></div>
                <div class="col-4 d-flex align-items-center justify-content-center">{{ user.username }}</div>
                <div class="col-6 d-flex align-items-center justify-content-center">{{ user.firstName }} {{ user.lastName }}
                </div>
            </div>
        </div>
    </div>
    <TherapistForm v-if="formState === 1" :user="selectedUser" @back="formState = 0" @therapist="storeTherapist" />
</template>

<style scoped lang="scss">
.user-pic {
    height: 60px;
    width: 60px;
    object-fit: cover;
    object-position: top;
}
</style>