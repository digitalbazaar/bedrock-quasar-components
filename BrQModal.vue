<template>
  <q-dialog
    ref="dialog"
    :value="value"
    :persistent="persistent"
    no-route-dismiss
    no-backdrop-dismiss
    @input="input">
    <q-card :style="style">
      <q-card-section class="q-pl-md q-pa-sm text-center">
        <div class="text-subtitle1">
          {{title}}
        </div>
        <q-space />
        <q-btn
          v-close-popup
          flat
          round
          dense
          class="absolute-right q-ma-xs"
          style="height: 34px"
          :disabled="loading">
          <q-icon
            name="fas fa-times"
            size="sm" />
        </q-btn>
      </q-card-section>
      <q-separator />
      <q-card-section>
        <slot />
        <div
          v-if="acceptLabel"
          class="row justify-between">
          <q-btn
            v-close-popup
            outline
            :disabled="loading"
            :label="cancelLabel"
            :color="cancelColor"
            class="col-4" />
          <q-btn
            :disabled="loading || disableAcceptButton"
            :loading="loading"
            :label="acceptLabel"
            :color="acceptColor"
            class="col-4"
            @click="accept()" />
        </div>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>
<script>
/*!
 * Copyright (c) 2018-2020 Digital Bazaar, Inc. All rights reserved.
 */

export default {
  name: 'BrQModal',
  props: {
    acceptColor: {
      type: String,
      required: false,
      default: 'primary',
    },
    acceptLabel: {
      type: String,
      required: false,
      default: null
    },
    cancelColor: {
      type: String,
      required: false,
      default: 'negative',
    },
    cancelLabel: {
      type: String,
      required: false,
      default: 'Cancel'
    },
    maximized: {
      type: Boolean,
      required: false,
      default: false,
    },
    minWidth: {
      type: String,
      required: false,
      default: '600px'
    },
    persistent: {
      type: Boolean,
      default: false,
      required: false
    },
    title: {
      type: String,
      required: true
    },
    value: {
      type: Boolean,
      required: true
    },
    disableAcceptButton: {
      type: Boolean,
      required: false
    }
  },
  data() {
    return {
      loading: false
    };
  },
  computed: {
    style() {
      return this.$q.screen.gt.xs ? {'min-width': this.minWidth} : '';
    }
  },
  methods: {
    async accept() {
      this.loading = true;
      try {
        await this.$emitExtendable('accept');
        this.hide();
      } finally {
        this.loading = false;
      }
    },
    async input($event) {
      this.$emit('input', $event);
      if(!$event) {
        await this.$emitExtendable('close');
      }
    },
    hide() {
      if(this.$refs.dialog) {
        this.$refs.dialog.hide();
      }
    },
    show() {
      if(this.$refs.dialog) {
        this.$refs.dialog.show();
      }
    }
  }
};
</script>

<style scoped>
</style>
