<script setup>
    import { ref, onMounted } from 'vue';
    let message = ref("");
    let messages = ref(["hello","world"]);

    let socket = null;

    onMounted(()=> {
        //console.log("mounted");
        //connect to web sockets server
        socket = new WebSocket("ws://localhost:3000/primus"); //dit is de localhost waarop je backend draait
        //listen to messages from web socket server
        socket.onmessage = (event) => {
            //console.log(event.data);
            let newMessage = JSON.parse(event.data);
            if(newMessage.action === "newMessage"){
                messages.value.push(newMessage.message);
            }
        }
    });

    const sendMessage = ()=>{
        //add message value to messages array
        //messages.value.push(message.value);
        let newMessage = {
            "message": message.value,
            "action": "newMessage"
        }
        //send message to web socket server
        socket.send(JSON.stringify(newMessage)); //stringify zet json om naar een string
        //empy input after sending
        message.value = "";
    }
</script>

<template>
  <div>
    <ul>
        <li v-for="m in messages"> {{ m }}</li>
    </ul>
    <div>
        <input v-model = "message" type="text" />
        <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<style scoped>
</style>