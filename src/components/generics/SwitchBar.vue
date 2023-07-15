<script>
export default {
    name: 'Switch Bar',
    props: {
        menus: Array,
        selectedMenu: String
    },
    emits: ['switch'],
    computed: {
        cols() {
            return 'row-cols-' + this.menus.length;
        }
    },
    methods: {
        menuClasses(i, menu) {
            let classes = '';
            if (i < this.menus.length - 1) classes += 'border-main-right';
            if (this.selectedMenu === menu) classes += ' selected';
            return classes;
        }
    }

}
</script>

<template>
    <div class="switch-row row bg-light rounded-4 mb-3 border-main w-100" :class="cols">
        <div class="col text-center py-2" v-for="(menu, i) in menus" :key="i" :class="menuClasses(i, menu)"
            @click="$emit('switch', menu)">
            <p class="mb-0">{{ menu }}</p>
        </div>
    </div>
</template>

<style scoped lang="scss">
.switch-row {
    overflow: hidden;
}

.border-main {
    border: 1px solid rgb(62, 14, 50);
}

.border-main-right {
    border-right: 1px solid rgb(62, 14, 50);
    ;
}

.col {
    transition: all 0.5s;
    font-size: 8px;
    font-weight: bold;
    color: rgb(62, 14, 50);
}

.selected {
    background-color: rgb(62, 14, 50);
    color: white;
}
</style>

