<template>
  <div class="hello">
    <div>
      <h3>Upload a video to transcode to mp4 (x264) and play!</h3>
      <video id="output-video" controls ></video><br/>
      <input type="file" id="uploader">
      <p id="message"></p>
    </div>
  </div>
</template>

<script>
import FFmpeg from '@ffmpeg/ffmpeg';

export default {
  name: 'ffmpeg',
  props: {
  },
  mounted() {
    const { createFFmpeg, fetchFile } = FFmpeg;
    const ffmpeg = createFFmpeg({
      // corePath: 'ffmpeg-core.js',
      corePath: 'https://unpkg.com/@ffmpeg/core@0.10.0/dist/ffmpeg-core.js',
      log: true,
    });
    const transcode = async ({ target: { files } }) => {
      const message = document.getElementById('message');
      // const { name } = files[0];
      message.innerHTML = 'Loading ffmpeg-core.js';
      await ffmpeg.load();
      ffmpeg.FS('writeFile', 'encodeURI(name)', await fetchFile(files[0]));
      message.innerHTML = 'Start transcoding';
      await ffmpeg.run('-i', 'encodeURI(name)', 'output.mp4');
      message.innerHTML = 'Complete transcoding';
      const data = ffmpeg.FS('readFile', 'output.mp4');

      const video = document.getElementById('output-video');
      video.src = URL.createObjectURL(new Blob([data.buffer], { type: 'video/mp4' }));
    }
    const elm = document.getElementById('uploader');
    elm.addEventListener('change', transcode);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
