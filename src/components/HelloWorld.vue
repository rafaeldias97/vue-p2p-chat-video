<template>
  <div class="hello">
    <label>Seu Id</label><br>
    <textarea v-model="yourId"></textarea><br>
    <label>Id Amigo</label><br>
    <textarea v-model="otherId"></textarea>
    <button @click="connect()">Conectar</button><br>

    <label>Entre com mensagem:</label><br>
    <textarea v-model="yourMessage"></textarea>
    <button @click="send">Enviar</button><br>
    <pre v-bind="messages"></pre>
  </div>
</template>

<script>
import Peer from 'simple-peer';

export default {
  data () {
    return {
      yourId: '',
      otherId: '',
      yourMessage: '',
      messages: '',
      peer: ''
    }
  },
  mounted () {
    this.createP2P()
  },
  methods: {
    createP2P () {
      navigator.getUserMedia({ video: true, audio: true }, (stream) => {
        const peer = new Peer({
          initiator: location.hash == "#create",
          trickle: false,
          stream: stream
        })
        this.peer = peer

        this.peer.on('signal', (data) => {
            this.yourId = JSON.stringify(data)
        })

        this.peer.on('data', (data) => {
            this.messages += data + '\n'
        })

        this.peer.on('stream', (stream) => {
          let video = document.createElement('video');
          document.body.appendChild(video);

          video.srcObject = stream;
          video.play();
        })
      }, (err) => {
          console.error(err)
      })
    },
    connect () {
        let otherId = JSON.parse(this.otherId)
        this.peer.signal(otherId)
    },
    send () {
        let yourMessage = this.yourMessage
        this.peer.send(yourMessage)
    }
  }
}
</script>

<style scoped>
</style>
