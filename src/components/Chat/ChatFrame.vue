<script>
export default {
    name: 'Chat Frame',
    props: {
        chat: Object,
        userRole: String,
    }
}
</script>

<template>
    <p>Chat with {{ this.userRole === 'therapist' ? chat.user.username : chat.therapist.therapistName }}</p>
</template>

<style scoped lang="scss"></style>