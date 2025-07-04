<script setup>
  import { useTemplateRef } from 'vue';
  import { useToast } from 'vue-toastification';
  import { HtmlDialog } from 'vue-html-dialog';
  import 'vue-html-dialog/vue-html-dialog.css';

  defineExpose({
    openDialog: () => dialogRef.value?.openDialog(),
    closeDialog: () => dialogRef.value?.closeDialog(),
    swapVideoSource: () => swapVideoSource()
  })

  const dialogRef = useTemplateRef('dialogRef');
  const videoRef = useTemplateRef('videoRef');
  const toast = useToast();

  const props = defineProps({
    url: {
      type: String,
      required: true,
      validator: val => val && typeof val === 'string' && val.length > 0
    },
    autoplay: {
      type: Boolean,
      default: false
    }
  });

  function swapVideoSource(){
    const video = videoRef.value;
    const sources = video.getElementsByTagName("source");
    var oldsrc = sources[0].src;
    var oldtype = sources[0].type;
    sources[0].src = sources[1].src;
    sources[0].type = sources[1].type;
    sources[1].src = oldsrc;
    sources[1].type = oldtype;
    video.load();
  }

  function stopVideo(){
    const video = videoRef.value;
    if (video) {
      video.pause();
      video.currentTime = 0;
    }
  }

  function onClose() {
    stopVideo();

    window.removeEventListener('ended', (event) => {
        stopVideo();
        dialogRef.value?.closeDialog();
      }, { once: true });
  }

  function onOpen() {
    const video = videoRef.value;

    if (!video) {
      console.error("Video element not found");
      toast.error("Video element not found. Please notify an Administrator.");
      return;
    }
    else {
      if (props.autoplay) {
        video.play();
      }

      video.addEventListener('ended', (event) => {
        stopVideo();
        dialogRef.value?.closeDialog();
      }, { once: true });
    }
  }

</script>

<template>
  <HtmlDialog ref="dialogRef" class="help-video-dialog" @open="onOpen" @close="onClose">
    <video ref="videoRef" nonce="ajJERjdDc1g5MlFadlZfdGdFIWI4dVchQ3o4Q3ZRYlQ=" class="video" controls>
      <source nonce="ajJERjdDc1g5MlFadlZfdGdFIWI4dVchQ3o4Q3ZRYlQ=" :src="props.url" type="video/mp4">
      Your browser does not support the video tag.
    </video>
  </HtmlDialog>
</template>

<style scoped>
  .help-video-dialog:deep(.dialog) {
    width: 100vw;
    height: 100vh;
    padding: 0;
    margin: 0;
    max-width: 100vw;
    max-height: 100vh;
  }

  .video {
    background-color: black;
    width: 100%;
    height: 100%;
  }

  .help-video-dialog:deep(.close-button) {
    font-size: 3.5rem;
    z-index: 1001;
    height: 5rem;
    width: 5rem;
    padding-block: 0;
    padding-inline: 0;
    top: -9px;
    right: 0px;
  }
</style>