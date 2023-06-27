<script>
export default {
    name: 'App Footer',
    props: {
        isLogged: Boolean,
        roles: Array,
        menuState: Number,
    },
    methods: {
        menuClick(i) {
            this.$emit('menu', i);
            window.scrollTo({
                top: 0,
                behavior: 'smooth'
            })
        }
    },
    emits: ['menu']

}
</script>

<template>
    <footer>
        <div class="container h-100">
            <nav v-if="!isLogged" class="row align-items-center h-100">
                <div class="col-3 text-center">
                    <i class="fa-brands fa-whatsapp"></i>
                    <address>+63 955 843 9963</address>
                </div>
                <div class="address col-6">
                    <address class="text-center">Blissfull Wellness Spa <br> Chinatown 2nd floor <br> Ormoc City, Leyte,
                        Philippines
                    </address>
                </div>
                <div class="col-3 text-center">
                    <a href="https://www.facebook.com/profile.php?id=100075865136535"><i
                            class="fa-brands fa-facebook"></i></a>
                </div>
            </nav>
            <nav v-if="isLogged" class="logged-footer row h-100 justify-content-around">
                <div class="col-2 d-flex flex-column align-items-center justify-content-center">
                    <i class="fa-solid fa-user mb-1"></i>
                    <small>Profile</small>
                </div>
                <div class="col-2 d-flex flex-column align-items-center justify-content-center">
                    <i class="fa-solid fa-envelope mb-1"></i>
                    <small>Mail</small>
                </div>
                <div class="col-2 d-flex flex-column align-items-center justify-content-center">
                    <i class="fa-solid fa-calendar mb-1"></i>
                    <small>Bookings</small>
                </div>
                <div class="col-2 d-flex flex-column align-items-center justify-content-center" @click="menuClick(6)"
                    :class="menuState === 6 ? 'selected' : ''">
                    <i class="fa-solid fa-cart-shopping mb-1"></i>
                    <small>Orders</small>
                </div>
                <div v-if="isLogged && roles.includes('admin')" @click="menuClick(5)"
                    class="col-2 d-flex flex-column align-items-center justify-content-center"
                    :class="menuState === 5 ? 'selected' : ''">
                    <i class="fa-solid fa-screwdriver-wrench"></i>
                    <small>Manage</small>
                </div>
            </nav>
        </div>
    </footer>
</template>

<style scoped lang="scss">
footer {
    position: sticky;
    height: 60px;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgb(62, 14, 50);
    box-shadow: 0 0 30px black;
    color: white;

    address {
        font-size: 10px;
        margin-bottom: 0;
    }

    .col-3 address {
        font-size: 8px;
    }

    a {
        color: white;
        text-decoration: none;
    }

    .logged-footer {

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
}
</style>