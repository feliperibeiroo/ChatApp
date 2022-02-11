<template>
  <div id="app">
    <!-- As a link -->
    <b-navbar class="navbar navbar-dark bg-primary">
      <b-navbar-brand href="#">Chat Online</b-navbar-brand>
      <span>Desenvolvido por Felipe Ribeiro</span>
    </b-navbar>
    <div class="content">
      <b-card class="msgbox">
        <ul>
          <message v-for="msg in messages" :key="msg+Math.random()" :color="msg.color" :msg="msg.msg"/>
        </ul>
      </b-card>
    </div>
    <div class="footer">
        <b-form-input v-model="text" type="text" autocomplete="off" @keydown.enter="enviar" placeholder="Digite sua mensagem aqui"></b-form-input>
        <b-button :disabled="!socket" @click="enviar" variant="primary">Enviar</b-button>
        <b-button @click="carregarSocket" variant="danger">Próximo</b-button>
      </div>
  </div>
</template>

<script>
import { io } from "socket.io-client";
import Message from '../components/Message.vue';
export default {
  components: { Message },
  data() {
    return {
      messages: [],
      anotherClient: null,
      text: '',
      socket: null,
    }
  },

  created() {
    this.carregarSocket()
  },

  methods: {
    carregarSocket() {
      if (this.socket) this.socket.disconnect(true);
      this.messages = []
      this.socket = io(process.env.HOST_SOCKET || 'http://127.0.0.1:8080', { transports : ['websocket'] })
      this.socket.on('connect', () => {
        this.messages.push({
          msg: 'Conectado ao servidor... Pronto para encontrar um parceiro...',
          color: 'green'
        })
      })
      this.socket.on('disconnected', (clientId) => {
        if (clientId==this.anotherClient) {
          this.messages.push({
            msg: 'O seu parceiro se desconectou :-(',
            color: 'red'
          })
          this.socket = null
        }
      })
      this.socket.on('joined', (anotherClient) => {
        this.anotherClient = anotherClient
        this.messages.push({
          msg: `Conectado com o usuário ${anotherClient}`,
          color: 'orange'
        })
      })
      this.socket.on('msg', (anotherClient, msg) => {
        this.anotherClient = anotherClient
        console.log(`Mensagem recebida de ${anotherClient}: ${msg}`);
        this.messages.push({
          msg: `Anônimo: ${msg}`
        })
      })
    },

    enviar() {
      if (this.text && this.socket) {
        this.socket.emit('msg', this.anotherClient, this.text);
        this.messages.push(
          {msg: `Você: ${this.text}`}
        )
        this.text = ''
      }
    }
  }
}
</script>

<style lang="scss">
#app {
  width: 100vw;
  display: grid;
  grid-template-rows: 10vh 75vh 10vh;
  grid-template-columns: 1fr;
  grid-template-areas: "a a"
                       "b b"
                       "c c";
  .navbar {
    grid-area: a;
    z-index: 100;
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
      overflow-y: scroll;
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
  }
  .footer {
      margin-top: 15px;
      padding-top: 10px;
      grid-area: c;
      z-index: 100;
      display: flex;
      flex-direction: row;
      align-items: flex-start;
      margin: 0 15px;

      button {
        margin-left: 10px;
        width: 100px;
      }
    }
}
</style>
