<script setup>
import { onMounted } from 'vue';

import { Client } from '@stomp/stompjs';

  const stompUrl = 'ws://localhost:3002/ws'


  const stompConfig = {
    brokerURL: stompUrl,
        // debug: function (str) {
        //   console.log(str);
        // },
    reconnectDelay: 5000,
        // heartbeatIncoming: 4000,
        // heartbeatOutgoing: 4000
  }

  const client = new Client(stompConfig)

function connect() {

  client.onConnect = function (frame)  {
    console.log('connection success')
    console.log(frame)

   

       const callback = function(message) {
        console.log('Hello from the other side')
        console.log(message)
       // called when the client receives a STOMP message from the server
         if (message.body) {
           alert("got message with body " + message.body)
         } else {
           alert("got empty message");
         }
       }


      //  // Explicit subscription id
       var mySubId = 'POS-TILL';
      client.subscribe(`/echo`, callback, { id: mySubId });
    
};

client.onStompError = function (frame) {
  console.log('ERROR')
  // Will be invoked in case of error encountered at Broker
  // Bad login/passcode typically will cause an error
  // Complaint brokers will set `message` header with a brief message. Body may contain details.
  // Compliant brokers will terminate the connection after any error
  console.log('Broker reported error: ' + frame.headers['message']);
  console.log('Additional details: ' + frame.body);
};

 client.activate();
}

const isConnected = client.connected
function sendMsg() {
     client.publish({
      destination: '/echo',
      body: "Hello, STOMP from client"
  })
}
 onMounted(() => {
    connect()
  })

</script>

<template>

Hello world
  {{isConnected}}


  <button @click="sendMsg">
  Send Msg
  </button>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
