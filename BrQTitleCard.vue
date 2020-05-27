<template>
  <q-card
    class="text-center"
    inline
    color="white">
    <h6
      class="q-my-none text-subtitle1 text-center q-py-sm q-px-md">
      {{title}}
      <slot name="head" />
    </h6>
    <q-space />
    <slot name="icon" />
    <q-separator />
    <q-card-section class="q-pa-none">
      <slot name="body" />
    </q-card-section>
  </q-card>
</template>

<script>
/*!
 * Copyright (c) 2019-2020 Digital Bazaar, Inc. All rights reserved.
 */

export default {
  name: 'BrQTitleCard',
  props: {
    title: {
      type: String,
      required: true
    }
  }
};
</script>

<style scoped>
</style>
