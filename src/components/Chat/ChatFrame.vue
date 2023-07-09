<script>
import axios from 'axios';

const baseApiUrl = 'http://localhost:8080/api/v1/'

export default {
    name: 'Chat Frame',
    data() {
        return {
            isOpen: false,
            message: {
                sender: this.userRole,
                chatId: this.chat.id,
                message: '',
            }
        }
    },
    props: {
        chat: Object,
        userRole: String,
        selectedChat: Number,
    },
    emits: ['open', 'message-sent'],
    methods: {
        imageUrl(user) {
            return 'http://localhost:8080/' + user.profilePic;
        },
        toggleOpen() {
            if (this.selectedChat === this.chat.id) this.$emit('open', null)
            else {
                this.scrollToBottom();
                this.$emit('open', this.chat.id);
            }

        },
        messageClass(message) {
            if (this.userRole === "user") {
                if (message.sender === "user") return 'align-self-end sent'
                if (message.sender === "therapist") return 'align-self-start received'
            }
            if (this.userRole === "therapist") {
                if (message.sender === "user") return 'align-self-start received'
                if (message.sender === "therapist") return 'align-self-end sent'
            }
        },
        sendMessage() {
            axios.post(baseApiUrl + 'chatmessages/store', this.message)
                .then(() => {
                    this.$emit('message-sent');
                    this.message.message = '';
                })
                .catch(e => console.log(e))
        },
        scrollToBottom() {
            const scrollContainer = this.$refs.chat;
            scrollContainer.scrollTo({
                top: scrollContainer.scrollHeight,
                behavior: 'smooth'
            });
        },
        messageDate(message) {
            const date = new Date(message.dateTime);
            const day = date.getDate() < 10 ? '0' + date.getDate() : date.getDate();
            const month = date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1;
            const year = date.getFullYear();
            const hours = date.getHours() < 10 ? '0' + date.getHours() : date.getHours();
            const minutes = date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes();
            return day + '/' + month + '/' + year + '   ' + hours + ':' + minutes;
        }
    },
    computed: {
        unreadMessages() {
            if (this.userRole === 'user') return this.chat.messages.filter(message => !message.seen && message.sender === "therapist")
            if (this.userRole === 'therapist') return this.chat.messages.filter(message => !message.seen && message.sender === "user")
        }
    },
    mounted() {
        this.scrollToBottom();
    },
    watch: {
        'chat.messages': {
            handler: function (newMessages, oldMessages) {
                if (newMessages.length !== oldMessages.length) {
                    this.$nextTick(() => {
                        console.log('boh');
                        this.scrollToBottom();
                    });
                }
            },
            deep: true
        },
        selectedChat: function (newSelectedChat) {
            if (this.chat.id === newSelectedChat) {
                this.unreadMessages.forEach(message => {
                    axios.patch(baseApiUrl + 'chatmessages/' + message.id + '/seen')
                        .then(() => this.$emit('message-sent'))
                        .catch(e => console.log(e))
                });
            }
        }
    }
}
</script>

<template>
    <div class="chat-frame mb-3">
        <div class="top d-flex align-items-center justify-content-between p-3" @click="toggleOpen">
            <div class="left d-flex align-items-center col-4">
                <img class="profile-pic me-3"
                    :src="userRole === 'therapist' ? imageUrl(chat.user) : imageUrl(chat.therapist.user)"
                    :alt="userRole === 'therapist' ? chat.user.username : chat.therapist.therapistName">
                <p class="mb-0">{{ this.userRole === 'therapist' ? chat.user.username : chat.therapist.therapistName }}</p>
            </div>
            <div class="center col-4 d-flex justify-content-center">
                <p class="notification mb-0 text-center" v-if="unreadMessages.length">{{ unreadMessages.length }}</p>
            </div>
            <div class="right col-4 text-end">
                <p class="mb-0" v-if="selectedChat === chat.id"><i class="fa-solid fa-chevron-up"></i></p>
            </div>
        </div>
        <div class="bottom" :class="selectedChat === chat.id ? '' : 'reduced'">
            <div class="chat p-3 d-flex flex-column" ref="chat">
                <div class="message mb-3 py-2 px-3 rounded-3 d-flex flex-column" v-for="message in chat.messages"
                    :class="messageClass(message)">
                    <p class="mb-1">{{ message.message }}</p>
                    <small>{{ messageDate(message) }}</small>
                </div>
            </div>
            <form @submit.prevent="sendMessage()" class="d-flex">
                <input class="p-1 col-10" type="text" placeholder="Text..." v-model="message.message">
                <button class="btn btn-chat col-2"><i class="fa-regular fa-paper-plane"></i></button>
            </form>
        </div>
        <div class="closing text-center" @click="toggleOpen">
            <i v-if="selectedChat != chat.id" class="fa-solid fa-chevron-down"></i>
        </div>
    </div>
</template>

<style scoped lang="scss">
.chat-frame {
    border-radius: 5px;
    box-shadow: 0 0 10px 2px rgba($color: #000000, $alpha: 0.5);
}

.top {
    color: white;
    background-color: rgb(62, 14, 50);
    border-radius: 5px 5px 0 0;

    .profile-pic {
        height: 30px;
        width: 30px;
        object-fit: cover;
        object-position: top;
        border-radius: 50%;
    }

    .notification {
        width: 20px;
        height: 20px;
        background-color: red;
        border-radius: 50%;
    }
}

.bottom {

    max-height: 1000px;
    overflow-y: hidden;
    transition: max-height 1s;

    &.reduced {
        max-height: 0;

    }

    .chat {
        background-color: white;
        height: 300px;
        overflow-y: auto;

        .message {

            box-shadow: 1px 1px 5px rgba($color: #000000, $alpha: 0.5);
            max-width: 60%;

            &.sent {
                background-color: rgb(62, 14, 50);
                color: white;

                small {
                    align-self: flex-end;
                }
            }

            &.received {
                color: rgb(62, 14, 50);
            }

            p {
                font-size: 14px;
            }

            small {
                font-size: 8px;
            }

        }
    }

    input {
        border: none;
        border-top: 1px solid rgb(62, 14, 50);
    }

    .btn-chat {
        border-radius: 0;
        color: white;
        background-color: rgb(62, 14, 50);
    }

}

.closing {
    min-height: 24px;
    background-color: rgb(62, 14, 50);
    color: white;
    border-radius: 0 0 5px 5px;
}
</style>