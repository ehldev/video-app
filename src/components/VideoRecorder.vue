<template>
  <div class="position-relative">
    <h2>Video app</h2>

    <video ref="videoContainer" autoplay class="main-video"></video>
    <!-- Si no se está grabando, muestra el botón para comenzar a grabar -->
    <!-- <div v-if="!isRecording">
      <button @click="startRecording">Comenzar a Grabar</button>
    </div> -->
    <!-- Si se está grabando, muestra un mensaje y un botón para detener la grabación -->
    <!-- <div v-else>
      <p>Grabando...</p>
      <button @click="stopRecording">Detener Grabación</button>
    </div> -->
    <!-- Muestra el video grabado si existe una URL válida -->

    <!-- <video v-if="videoURL" controls>
      <source :src="videoURL" type="video/webm" />
    </video> -->
  </div>
</template>

<script>
/* eslint-disable */
export default {
  data() {
    return {
      isRecording: false,
      videoURL: "", // Almacena la URL del video grabado
      mediaRecorder: null, // Objeto para la grabación de medios
      recordedChunks: [], // Almacena los fragmentos de video grabados
      recordingTimeout: null, // Temporizador para detener la grabación después de un tiempo
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    async init() {
      // Acceder a la cámara y el micrófono del dispositivo
      let stream = null;

      try {
        stream = await navigator.mediaDevices.getUserMedia({
          video: true,
          audio: true,
        });
      } catch (error) {
        console.error("Error al acceder a la cámara:", error);
      }

      // Mostrar en la pantalla
      this.$refs.videoContainer.srcObject = stream

      // Reiniciar los fragmentos grabados y configurar el mediaRecorder
      this.recordedChunks = [];
      this.mediaRecorder = new MediaRecorder(stream);
      this.mediaRecorder.ondataavailable = (event) => {
        if (event.data.size > 0) {
          this.recordedChunks.push(event.data); // Almacenar fragmentos válidos
        }
      };

      this.mediaRecorder.onstop = () => {
        // Convertir los fragmentos en un Blob y crear una URL para el video
        const blob = new Blob(this.recordedChunks, { type: "video/webm" });
        this.videoURL = URL.createObjectURL(blob);
        stream.getTracks().forEach((track) => track.stop()); // Detener la cámara y el micrófono
      };
    },
    startRecording() {
      this.mediaRecorder.start(); // Comenzar la grabación
      this.isRecording = true; // Cambiar el estado de grabación

      // Detener la grabación después de 10 segundos
      this.recordingTimeout = setTimeout(this.stopRecording, 10000);
    },
    stopRecording() {
      if (this.mediaRecorder && this.isRecording) {
        this.mediaRecorder.stop(); // Detener la grabación
        this.isRecording = false; // Cambiar el estado de grabación
        clearTimeout(this.recordingTimeout); // Limpiar el temporizador
      }
    },
  },
};
</script>

<style lang="scss">
.position-relative {
  position: relative;
}

.main-video {
  background-color: #191919;
  width: 100vw !important;
  height: 100vh !important;
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
</style>
