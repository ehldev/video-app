<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/> -->

    <div class="container">
      <VideoRecorder />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import VideoRecorder from '@/components/VideoRecorder.vue'

export default {
  name: 'HomeView',
  components: {
    VideoRecorder
  },
}
</script>
