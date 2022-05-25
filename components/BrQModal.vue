<template>
  <q-dialog
    ref="dialog"
    v-model="open"
    :persistent="persistent"
    no-route-dismiss
    no-backdrop-dismiss>
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
      <q-card-section class="q-pb-none">
        <slot />
      </q-card-section>
      <q-separator
        v-if="bottomSeparator"
        class="q-mb-md" />
      <div
        v-if="acceptLabel"
        class="row justify-between q-px-md q-pb-md">
        <div :class="fullWidthButtons ? 'col-6 q-pr-sm' : 'col-4'">
          <q-btn
            v-close-popup
            outline
            :disabled="loading"
            :label="cancelLabel"
            :color="cancelColor"
            class="full-width" />
        </div>
        <div :class="fullWidthButtons ? 'col-6 q-pl-sm' : 'col-4'">
          <q-btn
            :disabled="loading || disableAcceptButton"
            :loading="loading"
            :label="acceptLabel"
            :color="acceptColor"
            class="full-width"
            @click="accept()" />
        </div>
      </div>
    </q-card>
  </q-dialog>
</template>

<script>
import {emitExtendable} from '@digitalbazaar/vue-extendable-event';

/*!
 * Copyright (c) 2018-2022 Digital Bazaar, Inc. All rights reserved.
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
    modelValue: {
      type: Boolean,
      required: true
    },
    disableAcceptButton: {
      type: Boolean,
      required: false
    },
    bottomSeparator: {
      type: Boolean,
      required: false
    },
    fullWidthButtons: {
      type: Boolean,
      required: false
    }
  },
  emits: ['accept', 'close', 'update:modelValue'],
  setup(props, {emit}) {
    const open = computed({
      get: () => props.modelValue,
      set: async value => {
        emit('update:modelValue', value);
        if(!value) {
          await emitExtendable('close');
        }
      }
    });
    return {
      open
    };
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
