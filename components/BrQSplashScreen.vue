<template>
  <div class="column col-grow">
    <slot name="brand" />
    <div
      class="column q-mx-auto q-mt-none q-mb-sm"
      style="width: 300px">
      <q-carousel
        v-model="slide"
        transition-prev="fade"
        transition-next="fade"
        animated
        infinite
        :autoplay="autoplay"
        height="64px">
        <q-carousel-slide
          v-for="(caption, index) in captions"
          :key="index"
          :name="index"
          class="column no-wrap flex-center">
          <div class="q-mt-md text-center">
            {{caption}}
          </div>
        </q-carousel-slide>
      </q-carousel>
    </div>
    <div
      class="column q-mx-auto q-my-sm"
      style="width: 300px">
      <q-spinner-facebook
        class="q-mx-auto"
        color="primary"
        size="32px" />
    </div>
  </div>
</template>

<script>
/*!
 * Copyright (c) 2022 Digital Bazaar, Inc. All rights reserved.
 */
import {computed, ref, toRef, watch} from 'vue';

const DEFAULT_CAPTIONS = [
  'Loading...',
  'Reticulating splines...'
];

export default {
  name: 'BrQSplashScreen',
  props: {
    progress: {
      default: () => ({
        autoplay: 2000,
        slide: 0,
        captions: DEFAULT_CAPTIONS
      }),
      type: Object,
      required: false
    }
  },
  setup(props) {
    const progress = toRef(props, 'progress');
    const autoplay = computed(() => progress.value.autoplay ?? false);

    // map progress slide to local slide value
    const slide = ref(0);
    const watchedSlide = computed(() => progress.value.slide);
    watch(watchedSlide, value => slide.value = value);

    // handle non-autoplay captions that include progress percentage
    const captions = computed(() => {
      const {captions} = progress.value;
      if(progress.value.autoplay) {
        return captions;
      }
      return captions.map((c, i) => {
        const percent = Math.floor(i / captions.length * 100);
        return `${c} ... ${percent}%`;
      });
    });

    return {
      autoplay,
      slide,
      captions
    };
  }
};
</script>

<style lang="scss">
</style>
