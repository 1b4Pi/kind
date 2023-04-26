<template>
  <q-page class="fullscreen relative-position">
    <div
      v-for="(story, index) in formattedStories"
      :key="index"
      :class="story.classes"
      :style="story.positionStyle"
      ref="storyItems"
    >
      {{ story.keySentence }}
    </div>
    <div class="absolute-center bg-white flex flex-center text-center z-max">
      <div>K.I.N.D.</div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref, computed } from "vue";
import { onBeforeRouteLeave } from "vue-router";
import { Howl } from "howler";
import anime from "animejs/lib/anime.es.js";
import stories from "../data/stories.json";

export default defineComponent({
  name: "IndexPage",
  setup() {
    const storyItems = ref([]);
    let audio;

    const generateClasses = (index) => {
      const textWeights = [
        "thin",
        "light",
        "regular",
        "medium",
        "bold",
        "bolder",
      ];
      const textColors = [
        "primary",
        "secondary",
        "accent",
        "info",
        "warning",
        "error",
      ];
      // Add more class arrays here

      const classes = [
        `text-weight-${textWeights[index % textWeights.length]}`,
        `text-color-${textColors[index % textColors.length]}`,
      ];

      if (index % 3 === 0) {
        classes.push("text-italic text-h1");
      }
      if (index % 5 === 0) {
        classes.push("text-white");
      }

      // Add more conditions for classes here

      // Generate random top and left position values (between 0% and 100%)
      const top = Math.random() * 100;
      const left = Math.random() * 100;
      const positionStyle = `position: absolute; top: ${top}%; left: ${left}%;`;

      return { classes, positionStyle };
    };

    const formattedStories = computed(() =>
      stories.map((story, index) => {
        const { classes, positionStyle } = generateClasses(index);
        return { ...story, classes, positionStyle };
      })
    );

    const animateStoryItems = () => {
      storyItems.value.forEach((item, i) => {
        anime
          .timeline({
            targets: item,
            easing: "easeInOutQuad",
          })
          .add({
            scale: [0, anime.random(0.1, 4)], // Random end scale between 0.1 and 3
            duration: anime.random(1000 + i * 200, 3000 + i * 200),
            delay: anime.random(i * 200, 500 + i * 200),
            loop: true,
          })
          .add({
            scale: [anime.random(0.1, 4), 0], // Random start scale between 0.1 and 3
            duration: anime.random(1000 + i * 200, 3000 + i * 200),
            delay: anime.random(i * 200, 500 + i * 200),
            loop: true,
          });
      });
    };

    const playAudio = () => {
      if (audio) {
        audio.stop();
      }

      audio = new Howl({
        src: [`${import.meta.env.BASE_URL}audio/Shared_Umbrella.m4a`],
        format: ["m4a"],
      });

      audio.play();
    };

    onBeforeRouteLeave((to, from, next) => {
      if (audio) {
        audio.stop();
      }
      next();
    });

    onMounted(() => {
      animateStoryItems();
      try {
        playAudio();
      } catch (error) {
        console.log("cant play without click");
      }
    });

    return {
      stories,
      formattedStories,
      storyItems,
      generateClasses,
    };
  },
});
</script>

<style scoped>
.story-item {
  position: absolute;
  white-space: nowrap;
}
</style>
