<script>
export default {
    name: 'Therapist Form',
    data() {
        return {
            therapist: {
                therapistName: this.formState === 2 ? this.user.therapistName : '',
                description: this.formState === 2 ? this.user.description : '',
                userId: this.user.id,
                massages: this.formState === 2 ? this.user.massages.map(massage => massage.id) : [],
            },
        }
    },
    computed: {
        profilePicUrl() {
            return this.formState === 2 ? this.user.user.profilePic : this.user.profilePic;
        },
        firstName() {
            return this.formState === 2 ? this.user.user.firstName : this.user.firstName;
        },
        lastName() {
            return this.formState === 2 ? this.user.user.lastName : this.user.lastName;
        },
        userName() {
            return this.formState === 2 ? this.user.user.username : this.user.username;
        },
    },
    props: {
        user: Object,
        massages: Array,
        formState: Number
    },
    emits: ['back', 'therapist'],
    methods: {
        isChecked(massage) {
            const massagesIds = this.therapist.massages.map(massage => massage.id)
            return massagesIds.includes(massage.id);
        },
        selectAll() {
            const massagesId = this.massages.map(massage => massage.id);
            massagesId.forEach(id => {
                if (!this.therapist.massages.includes(id)) {
                    this.therapist.massages.push(id);
                }
            })
        }
    }
}
</script>

<template>
    <form @submit.prevent="$emit('therapist', therapist)">
        <div class="card p-5 d-flex flex-column align-items-center">
            <h2 class="text-center mb-3"><span v-if="formState === 1">PROMOTE {{ user.username.toUpperCase() }}
                    TO</span><span v-else>EDIT</span> THERAPIST</h2>
            <img :src="profilePicUrl" :alt="userName" class="user-pic rounded-circle img-fluid border mb-3">
            <label for="firstname">First name</label>
            <input class="mb-3" type="text" name="firstname" id="firstname" :value="firstName" disabled>
            <label for="lastname">Last name</label>
            <input class="mb-3" type="text" name="lastname" id="lastname" :value="lastName" disabled>
            <label for="therapistName">Name as Therapist</label>
            <input class="mb-3" type="text" name="therapistName" id="therapistName" v-model="therapist.therapistName">
            <label for="description">Description</label>
            <textarea class="mb-3" name="description" id="description" rows="10" v-model="therapist.description"></textarea>
            <h4 class="text-center mb-2">Select massages:</h4>
            <button type="button" class="btn btn-primary mb-3" @click="selectAll()">SELECT ALL</button>
            <div class="massages row border rounded-2 p-3 mb-4">
                <div class="col-6" v-for="massage in massages">
                    <input class="me-2" type="checkbox" :value="massage.id" :id="'massage' + massage.id"
                        :checked="isChecked(massage)" v-model="therapist.massages"><label :for="'massage' + massage.id">{{
                            massage.name }}</label>
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

.massages {
    max-height: 100px;
    overflow-y: auto;
}
</style>