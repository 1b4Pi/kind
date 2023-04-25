<!-- src/pages/StoryPage.vue -->

<template>
  <q-page>
    <h1>{{ story.title }}</h1>
    <p>{{ story.subhead }}</p>
    <p>{{ story.keySentence }}</p>
    <p>{{ story.bodyCopy }}</p>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import { useRoute, useRouter, onBeforeRouteLeave } from "vue-router";
import { useQuasar } from "quasar";
import stories from "../data/stories.json";
import { Howl } from "howler";

export default defineComponent({
  name: "StoryPage",

  setup() {
    const $q = useQuasar();
    const route = useRoute();
    const router = useRouter();
    const story = ref({});
    let audio;

    // search stories for match
    const getStoryByKeySentence = (keySentence) => {
      const foundStory = stories.find((s) => s.keySentence === keySentence);
      if (foundStory) {
        // Populate `story` with `foundStory`
        story.value = foundStory;
      } else {
        // Handle the case where no story is found
        router.push({ name: "error" });
      }
    };

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
      getStoryByKeySentence(route.params.keySentence);
      playAudio();
      $q.notify({
        message: "Say `Search Kind` to go back",
        position: "bottom",
      });
    });

    return {
      story,
    };
  },
});
</script>
