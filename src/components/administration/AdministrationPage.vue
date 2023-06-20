<script>
export default {
    name: 'Administration Page',
    emits: ['administration']
}
</script>

<template>
    <div class="row">
        <div class="col-12 mb-3">
            <div class="card p-5 d-flex align-items-center justify-content-center" @click="$emit('administration', 1)">
                <i class="fa-solid fa-user fa-3x mb-3"></i>
                <h4 class="mb-0">Therapists</h4>
            </div>
        </div>
        <div class="col-12 mb-3">
            <div class="card p-5 d-flex align-items-center justify-content-center" @click="$emit('administration', 2)">
                <i class="fa-solid fa-hand-sparkles fa-3x mb-3"></i>
                <h4 class="mb-0">Massages</h4>
            </div>
        </div>
        <div class="col-12 mb-3">
            <div class="card p-5 d-flex align-items-center justify-content-center" @click="$emit('administration', 3)">
                <i class=" fa-solid fa-jar fa-3x mb-3"></i>
                <h4 class="mb-0">Products</h4>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss"></style>

