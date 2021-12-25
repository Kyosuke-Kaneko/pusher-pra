<template>
    <div>
        <ul>
            <li v-for="(message, key) in messages" :key="key">
                <strong>{{ message.user.name }}</strong>
                {{ message.message }}
            </li>
        </ul>
        <input v-model="text" />
        <button @click="postMessage(text)" :disabled="!textExists">送信</button>
    </div>
</template>
 
<script src="https://js.pusher.com/7.0/pusher.min.js"></script>

<script>
export default {
    data() {
        return {
            text: "",
            messages: []
        };
    },
    computed: {
        textExists() {
            return this.text.length > 0;
        }
    },
    created() {
        this.fetchMessages();
        Echo.private("chat").listen("MessageSent", e => {
            this.messages.push({
                message: e.message.message,
                user: e.user
            });
        });
    },
    methods: {
        fetchMessages() {
            axios.get("/messages").then(response => {
                this.messages = response.data;
            });
        },
        postMessage(text) {
            console.log(text);
            axios.post("/messages", { message: text }).then(response => {
                console.log(response);
                this.text = "";
            });
        }
    }
};
</script>