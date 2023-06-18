<script>
export default {
    name: 'Log in form',
    data() {
        return {
            user: {
                username: '',
                password: ''
            }
        }
    },
    methods: {
        logIn() {
            if (this.user.username && this.user.password) {
                this.$emit('login', this.user);
                this.$emit('login-close');
            }
        }
    },
    emits: ['login', 'login-close']
}
</script>

<template>
    <div class="overlay container d-flex align-items-center justify-content-center">
        <div class="login card p-5">
            <form @click.prevent="logIn" class="login-foorm d-flex flex-column align-items-center">
                <h3 class="mb-4">Insert your data</h3>
                <label for="email">Username</label>
                <input type="text" name="username" id="username" class="mb-3" v-model="user.username">
                <label for="password">Password</label>
                <input type="password" name="password" id="password" class="mb-5" v-model="user.password">
                <button class="btn btn-primary">Login</button>
                <button type="button" class="btn btn-closure" @click="$emit('login-close')"><i
                        class="fa-solid fa-xmark"></i></button>
            </form>
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
    }
}
</style>