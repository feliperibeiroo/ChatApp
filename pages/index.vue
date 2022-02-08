<template>
  <b-button @click="enviarMsg" variant="outline-primary">Button</b-button>
</template>

<script>
import { io } from "socket.io-client";
export default {
  data(){
    return {
      socket: io("http://127.0.0.1:8080", { transports : ['websocket'] })
    }
  },
  methods: {
    enviarMsg() {
      this.socket.emit('msg', 'Oi servidor!')
    }
  }
}
</script>
