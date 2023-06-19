<script>
export default {
    name: 'AppHeader',
    data() {
        return {
            menuIsOpen: false
        }
    },
    methods: {
        toggleMenu() {
            this.menuIsOpen = !this.menuIsOpen;
        },
        menuClick(i, menu = true) {
            this.$emit('menu', i);
            if (menu) this.toggleMenu();
            window.scrollTo({
                top: 0,
                behavior: 'smooth'
            })
        }
    },
    props: {
        isLogged: Boolean,
        roles: Array
    },
    emits: ['menu']
}
</script>

<template>
    <header>
        <nav class="nav-top d-flex align-items-center">
            <nav class="container d-flex justify-content-between align-items-center">
                <nav class="nav-left p-2">
                    <div class="logo" @click="menuClick(1, false)">
                        <p class="text-start">Blissfull</p>
                        <p class="text-end">Wellness</p>
                        <p class="text-center">Spa</p>
                    </div>
                </nav>
                <nav class="nav-right p-2">
                    <button class="btn cs-btn-primary" @click="toggleMenu">
                        <i class="fa-solid fa-bars fa-2x"></i>
                    </button>
                </nav>
            </nav>
        </nav>
        <nav v-if="menuIsOpen" class="menu-window text-center mb-0">
            <a class="mb-0 py-1" @click="menuClick(1)">Home</a>
            <a class="mb-0 py-1" @click="menuClick(2)">Therapists</a>
            <a class="mb-0 py-1" @click="menuClick(3)">Massages</a>
            <a class="mb-0 py-1" @click="menuClick(4)">Products</a>
            <a v-if="isLogged && roles.includes('admin')" class="mb-0 py-1" @click="menuClick(5)">Administration</a>
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
    z-index: 2;
    box-shadow: 0 0 30px black;

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
    }

    .nav-left {
        color: white;
    }

    .nav-right {
        position: relative;

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