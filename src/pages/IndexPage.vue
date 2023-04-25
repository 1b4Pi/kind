<template>
  <q-page class="flex flex-center">
    <img
      alt="Quasar logo"
      src="~assets/quasar-logo-vertical.svg"
      style="width: 200px; height: 200px"
    />
  </q-page>
</template>

<script>
import { defineComponent, onMounted } from "vue";
import { onBeforeRouteLeave } from "vue-router";
import { Howl } from "howler";

export default defineComponent({
  name: "IndexPage",
  setup() {
    let audio;

    // audio setup
    const playAudio = () => {
      if (audio) {
        audio.stop();
      }

      audio = new Howl({
        // src: [`${import.meta.env.BASE_URL}audio/${story.value.audio}.mp3`],
        src: [`${import.meta.env.BASE_URL}audio/Shared_Umbrella.m4a`],
        format: ["m4a"],
      });

      audio.play();
    };

    // stop playing if we leave this page
    onBeforeRouteLeave((to, from, next) => {
      if (audio) {
        audio.stop();
      }
      next();
    });

    // when the page is ready, do the following immediately...
    onMounted(() => {
      try {
        playAudio();
      } catch (error) {
        console.log("cant play without click");
      }
    });

    return {};
  },
});
</script>
