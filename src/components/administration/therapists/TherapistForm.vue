<script>
export default {
    name: 'Therapist Form',
    data() {
        return {
            therapist: {
                therapistName: '',
                description: '',
                userId: this.user.id
            }
        }
    },
    props: {
        user: Object
    },
    emits: ['back', 'therapist']
}
</script>

<template>
    <form @submit.prevent="$emit('therapist', therapist)">
        <div class="card p-5 d-flex flex-column align-items-center">
            <h2 class="text-center mb-3">PROMOTE {{ user.username.toUpperCase() }} TO THERAPIST</h2>
            <img :src="user.profilePic" :alt="user.username" class="user-pic rounded-circle img-fluid border mb-3">
            <label for="firstname">First name</label>
            <input class="mb-3" type="text" name="firstname" id="firstname" :value="user.firstName" disabled>
            <label for="lastname">Last name</label>
            <input class="mb-3" type="text" name="lastname" id="lastname" :value="user.lastName" disabled>
            <label for="therapistName">Name as Therapist</label>
            <input class="mb-3" type="text" name="therapistName" id="therapistName" v-model="therapist.therapistName">
            <label for="description">Description</label>
            <textarea class="mb-3" name="description" id="description" rows="10" v-model="therapist.description"></textarea>
            <div class="buttons d-flex col-12 justify-content-center">
                <button type="button" class="btn btn-secondary back" @click="$emit('back')"><i
                        class="fa-solid fa-arrow-left"></i></button>
                <button class="btn btn-success">Confirm</button>
            </div>
        </div>
    </form>
</template>

<style scoped lang="scss">
.user-pic {
    height: 60px;
    width: 60px;
    object-fit: cover;
    object-position: top;
}

.buttons {
    position: relative;

    .btn.back {
        position: absolute;
        left: 0;
    }
}
</style>