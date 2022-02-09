<template>
  <div id="app">
    <!-- As a link -->
    <b-navbar class="navbar navbar-dark bg-primary">
      <b-navbar-brand href="#">Chat Online</b-navbar-brand>
      <span>Desenvolvidor por Felipe Ribeiro</span>
    </b-navbar>
    <div class="content">
      <b-card class="msgbox">
        <ul>
          <li>msg1</li>
          <li>msg2</li>
          <li>msg3</li>
          <li>msg4</li>
          <li>msg5dfbdbdgdfgf</li>
          <li>msg6</li>
        </ul>
      </b-card>
      <div class="footer">
        <b-form-input v-model="text" placeholder="Digite sua mensagem aqui"></b-form-input>
        <b-button @click="enviar" variant="primary">Enviar</b-button>
        <b-button variant="danger">Desligar</b-button>
      </div>
    </div>
  </div>
</template>

<script>
import { io } from "socket.io-client";
export default {
  data() {
    return {
      text: '',
      socket: io(process.env.HOST_SOCKET, { transports : ['websocket'] })
    }
  },

  methods: {
    enviar() {
      if (this.text) {
        io.emit('msg', this.text);
        this.text = ''
      }
    }
  }
}
</script>

<style lang="scss">
#app {
  height: 100vh;
  width: 100vw;
  display: grid;
  grid-template-rows: 70px auto 70px;
  grid-template-columns: 1fr;
  grid-template-areas: "a a"
                       "b b"
                       "c c";
  .navbar {
    grid-area: a;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    padding: 0 20px;
    color: white;
  }
  .content {
    padding: 15px;
    .msgbox {
      grid-area: b;
      display: flex;
      height: 100%;
      ul {
        list-style: none;
        padding: 0;
        li {
          padding: 5px;
        }
        li:nth-child(2n-1) {
          background-color: rgb(228, 226, 226);
        }
        * {
          overflow-wrap: break-word;
        }
      }
    }

    .footer {
      margin-top: 15px;
      display: grid;
      grid-template-columns: auto 100px 100px;
      column-gap: 10px;
    }

  }
}
</style>
