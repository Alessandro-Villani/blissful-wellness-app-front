<script>
export default {
    name: 'Signin Form',
    data() {
        return {
            user: {
                username: '',
                password: '',
                firstName: '',
                lastName: '',
                dateOfBirth: '',
            },
            profilePic: null,
            loadedProfilePicUrl: null,
        }
    },
    methods: {
        handleFileChange(event) {
            const file = event.target.files[0];
            this.profilePic = file;
            this.loadedProfilePicUrl = this.profilePic ? URL.createObjectURL(this.profilePic) : null;
        },
        signIn() {
            if (this.user.username && this.user.password && this.user.firstName && this.user.lastName && this.user.dateOfBirth) {
                const sentUser = {
                    user: this.user,
                    file: this.profilePic
                }
                this.$emit('signin', sentUser);
            }
        }
    },
    emits: ['signin', 'signin-close']
}
</script>

<template>
    <div class="overlay container d-flex align-items-center justify-content-center">
        <div class="signin card p-5">
            <img class="profile-pic"
                :src="profilePic ? loadedProfilePicUrl : 'https://t3.ftcdn.net/jpg/05/71/08/24/360_F_571082432_Qq45LQGlZsuby0ZGbrd79aUTSQikgcgc.jpg'"
                alt="">
            <h2 class="text-center">INSERT YOUR DATA TO REGISTER</h2>
            <form @submit.prevent="signIn" class="signin-form row">
                <div class="col-12 d-flex flex-column mb-2">
                    <label for="username">Username</label>
                    <input type="text" name="username" id="username" v-model="user.username">
                </div>
                <div class="col-12 d-flex flex-column mb-2">
                    <label for="username">Password</label>
                    <input type="password" name="password" id="password" v-model="user.password">
                </div>
                <div class="col-12 d-flex flex-column mb-2">
                    <label for="firstName">First Name</label>
                    <input type="text" name="firstName" id="firstName" v-model="user.firstName">
                </div>
                <div class="col-12 d-flex flex-column mb-2">
                    <label for="firstName">Last Name</label>
                    <input type="text" name="lastName" id="lastName" v-model="user.lastName">
                </div>
                <div class="col-12 d-flex flex-column mb-2">
                    <label for="dateOfBirth">Date of Birth</label>
                    <input type="date" name="dateOfBirth" id="dateOfBirth" v-model="user.dateOfBirth">
                </div>
                <div class="col-12 d-flex flex-column mb-5">
                    <label for="profilePic">Profile picture</label>
                    <input type="file" name="profilePic" id="profilePic" @change="handleFileChange">
                </div>
                <div class="col-12 text-center">
                    <button class="btn btn-success">Confirm</button>
                </div>
            </form>
            <button type="button" class="btn btn-closure" @click="$emit('signin-close')"><i
                    class="fa-solid fa-xmark"></i></button>
        </div>
    </div>
</template>

<style scoped lang="scss">
.overlay {
    background-color: rgba($color: #000000, $alpha: 0.5);
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 5;

    .card {
        position: relative;

        .btn-closure {
            position: absolute;
            top: 5px;
            right: 10px;
            font-size: 18px;

            &:hover {
                color: red;
            }
        }

        .profile-pic {
            position: absolute;
            top: -50px;
            left: 50%;
            transform: translateX(-50%);
            height: 100px;
            width: 100px;
            border-radius: 50%;
            object-fit: cover;
            object-position: center;
        }
    }
}
</style>
