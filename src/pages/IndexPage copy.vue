<template>
  <q-page class="flex flex-center">
    <img
      alt="Quasar logo"
      src="~assets/quasar-logo-vertical.svg"
      style="width: 200px; height: 200px"
    />
    <q-page-sticky position="bottom-right" :offset="[18, 18]">
      <q-btn
        round
        color="accent"
        icon="arrow_forward"
        @click="toggleRecognition()"
      />
    </q-page-sticky>
  </q-page>
</template>

<script>
import { defineComponent, ref, watch } from "vue";
import { useRouter } from "vue-router";
import { useSpeechRecognition } from "@vueuse/core";
import stories from "../data/stories.json";

export default defineComponent({
  name: "IndexPage",
  components: {},
  setup() {
    const router = useRouter();
    const isRecording = ref(false);
    const lastResult = ref("");

    const searchForKeySentenceMatch = (result) => {
      const keySentence = result.toLowerCase();
      const story = stories.find(
        (s) => s.keySentence.toLowerCase() === keySentence
      );

      if (story) {
        router.push({
          path: `/story/${encodeURIComponent(story.keySentence)}`,
        });
      }
    };

    const { start, stop, error, isListening } = useSpeechRecognition({
      onResult: (result) => {
        lastResult.value = result;
      },
      onError: (error) => {
        console.error("Speech recognition error:", error);
      },
      continuous: true,
    });

    watch(lastResult, (newValue) => {
      if (!isRecording.value) {
        console.log("watch lastResult");
        searchForKeySentenceMatch(newValue);
      }
    });

    const toggleRecognition = () => {
      if (isListening.value) {
        stop();
        isRecording.value = false;
        console.log("K.I.N.D. is not listening");
      } else {
        start();
        isRecording.value = true;
        console.log("K.I.N.D. is listening");
      }
    };

    return {
      isRecording,
      toggleRecognition,
    };
  },
});
</script>
