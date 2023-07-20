<script>
export default {
    name: 'AppHeader',
    methods: {
        menuClick(i) {
            this.$emit('menu', i);
            window.scrollTo({
                top: 0,
                behavior: 'smooth'
            })
        }
    },
    props: {
        isLogged: Boolean,
        roles: Array,
        menuState: Number,
        user: Object,
    },
    emits: ['menu', 'login-form', 'signin-form', 'logout']
}
</script>

<template>
    <header :class="isLogged ? 'logged' : ''">
        <nav class="nav-top d-flex align-items-center">
            <nav class="container d-flex justify-content-center align-items-center">
                <nav class="col-4 p-2">
                    <div v-if="isLogged && user" class="user d-flex flex-row align-items-center">
                        <img class="user-img img-fluid rounded-circle me-2"
                            :src="'http://localhost:8080/' + user.profilePic" :alt="user.username">
                        <p class="mb-0">{{ user.username }}</p>
                    </div>
                </nav>
                <nav class="nav-center col-4 d-flex justify-content-center align-items-center p-2">
                    <div class="logo" @click="menuClick(1, false)">
                        <p class="text-start">Blissfull</p>
                        <p class="text-end">Wellness</p>
                        <p class="text-center">Spa</p>
                    </div>
                </nav>
                <nav class="nav-right buttons col-4 d-flex justify-content-end">
                    <button v-if="!isLogged" class="btn btn-primary me-2" @click="$emit('login-form')">Login</button>
                    <button v-if="!isLogged" class="btn btn-secondary" @click="$emit('signin-form')">Sign in</button>
                    <button v-if="isLogged" class="btn btn-secondary" @click="$emit('logout')">Log out</button>
                </nav>
            </nav>
        </nav>
        <nav v-if="isLogged" class="nav-bottom d-flex align-items-center">
            <div class="col-3 d-flex flex-column align-items-center justify-content-center" @click="menuClick(1)"
                :class="menuState === 1 ? 'selected' : ''">
                <i class="fa-solid fa-house mb-1"></i>
                <small>Home</small>
            </div>
            <div class="col-3 d-flex flex-column align-items-center justify-content-center" @click="menuClick(2)"
                :class="menuState === 2 ? 'selected' : ''">
                <i class="fa-solid fa-person mb-1"></i>
                <small>Therapists</small>
            </div>
            <div class="col-3 d-flex flex-column align-items-center justify-content-center" @click="menuClick(3)"
                :class="menuState === 3 ? 'selected' : ''">
                <i class="fa-solid fa-hand-sparkles mb-1"></i>
                <small>Massages</small>
            </div>
            <div class="col-3 d-flex flex-column align-items-center justify-content-center" @click="menuClick(4)"
                :class="menuState === 4 ? 'selected' : ''">
                <i class="fa-solid fa-jar mb-1"></i>
                <small>Products</small>
            </div>
        </nav>
    </header>
</template>

<style scoped lang="scss">
header {
    background-color: rgb(62, 14, 50);
    position: sticky;
    top: 0;
    height: 75px;
    left: 0;
    right: 0;
    z-index: 10;
    box-shadow: 0 0 30px black;

    &.logged {
        height: 130px;

        .nav-top {
            height: 65%;
        }

    }

    .logo {
        width: 55px;
        height: 55px;
        border: 3px solid rgb(253, 1, 165);
        background-color: white;
        border-radius: 50%;
        padding: 7px;
        box-shadow: 0 0 2px black;
        cursor: pointer;

        p {
            margin-bottom: 2px;
            font-size: 8px;
            font-weight: bold;
            color: rgb(253, 1, 165);
            text-shadow: 0.2px 0.2px black;
        }
    }

    .nav-top {
        height: 100%;

        .user {
            color: white;

            p {
                font-size: 12px;
            }

            .user-img {
                height: 40px;
                width: 40px;
                object-fit: cover;
                object-position: center;
                box-shadow: 0 0 2px black;
            }

        }

        .buttons button {
            font-size: 10px;
        }
    }


    .nav-bottom {
        color: white;

        i {
            font-size: 20px;
        }

        small {
            font-size: 12px;
        }

        .selected {
            color: rgb(253, 1, 165);

        }
    }

    .cs-btn-primary {

        background-color: transparent;
        color: rgb(252, 232, 234);

    }
}

.menu-window {
    color: white;
    border-top: 2px solid white;

    a {

        display: block;
        background-color: rgb(62, 14, 50);
        border-bottom: 2px solid white;
        color: white;
        text-decoration: none;
        transition: all 0.5s;

        &:hover {
            filter: brightness(1.5);
        }
    }
}
</style>