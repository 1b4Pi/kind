<template>
  <q-layout view="lHh Lpr lFf">
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import { useQuasar } from "quasar";
import stories from "../data/stories.json";
import Fuse from "fuse.js";

export default defineComponent({
  name: "MainLayout",
  setup() {
    const $q = useQuasar();
    const router = useRouter();
    const lastResult = ref("");

    const fuse = new Fuse(stories, {
      keys: ["keySentence"],
      includeScore: true,
      threshold: 0.4,
    });

    const searchForKeySentenceMatch = (keySentence) => {
      console.log("searchForKeySentenceMatch called:", keySentence);

      if (keySentence.trim().toLowerCase() === "search kind") {
        console.log("Search kind command detected");
        router.push({ path: "/" });
        return;
      }

      const results = fuse.search(keySentence);

      if (results.length > 0 && results[0].score < 0.4) {
        const story = results[0].item;
        router.push({
          path: `/story/${encodeURIComponent(story.keySentence)}`,
        });
      } else {
        $q.notify({
          message: "No kind found",
          position: "center",
          type: "negative",
          icon: "warning",
        });
      }
    };

    const SpeechRecognition =
      window.SpeechRecognition || window.webkitSpeechRecognition;

    const recognition = new SpeechRecognition();
    recognition.continuous = true;
    recognition.interimResults = true;
    recognition.maxAlternatives = 1;

    recognition.onresult = (event) => {
      const transcript = event.results[event.results.length - 1][0].transcript;
      const isFinal = event.results[event.results.length - 1].isFinal;

      if (isFinal) {
        lastResult.value = transcript;
        console.log("Final transcript:", lastResult.value);
        searchForKeySentenceMatch(lastResult.value);
      }
    };

    recognition.onerror = (event) => {
      console.error("Speech recognition error:", event);
    };

    recognition.start();
    console.log("K.I.N.D. is listening");

    onMounted(() => {
      $q.notify({
        message: "Speak Kind",
        position: "center",
      });
    });

    return {};
  },
});
</script>
