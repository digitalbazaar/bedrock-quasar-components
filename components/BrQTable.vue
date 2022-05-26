<template>
  <div class="column items-center">
    <q-table
      :rows="data"
      :columns="columns"
      :loading="loading"
      :row-key="rowKey"
      :flat="$q.screen.gt.sm"
      :grid="$q.screen.lt.md"
      class="full-width">
      <template #body="props">
        <q-tr :props="props">
          <q-td
            v-for="col in columns"
            :key="col.field"
            :props="props">
            <div>
              <div v-if="col.type === 'button'">
                <q-btn
                  outline
                  square
                  no-caps
                  :color="props.row.rowColor ?
                    props.row.rowColor : col.buttonColor"
                  :label="col.buttonLabel"
                  @click="handleButton(col.field, props.row)" />
              </div>
              <div v-else-if="col.type === 'chip'">
                <q-chip
                  square
                  text-color="white"
                  class="q-ma-none q-pa-none"
                  style="min-width: 75px; height: 36px"
                  :style="{'background-color': props.row[col.field]}" />
              </div>
              <div
                v-else
                :class="props.row.rowColor ? 'text-' + props.row.rowColor : ''">
                {{props.row[col.field]}}
              </div>
            </div>
          </q-td>
        </q-tr>
      </template>
      <!-- Mobile Support Prop -->
      <template #item="props">
        <div
          class="q-pt-xs q-px-sm q-pb-sm col-xs-12 col-sm-6
            col-md-4 col-lg-3 profile-card">
          <q-card
            bordered
            flat>
            <q-list class="q-py-sm">
              <q-item
                v-for="col in props.cols"
                :key="col.name">
                <q-item-section class="text-center">
                  <q-item-label caption>
                    {{col.label}}
                  </q-item-label>
                  <div v-if="col.type === 'button'">
                    <q-btn
                      outline
                      square
                      no-caps
                      class="q-mt-xs"
                      :color="props.row.rowColor ? props.row.rowColor :
                        col.buttonColor"
                      :label="col.buttonLabel"
                      @click="handleButton(col.field, props.row)" />
                  </div>
                  <div v-else-if="col.type === 'chip'">
                    <q-chip
                      square
                      text-color="white"
                      class="q-mx-none q-mb-none q-pa-none"
                      style="min-width: 75px; height: 36px"
                      :style="{'background-color': props.row[col.field]}" />
                  </div>
                  <q-item-label
                    v-else
                    :class="props.row.rowColor ? 'text-' +
                      props.row.rowColor : ''"
                    style="font-size: 13px">
                    {{props.row[col.field]}}
                  </q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-card>
        </div>
      </template>
    </q-table>
  </div>
</template>

<script>
/*!
 * Copyright (c) 2020-2022 Digital Bazaar, Inc. All rights reserved.
 */
export default {
  name: 'BrQTable',
  props: {
    loading: {
      type: Boolean,
      default: false,
      required: false
    },
    columns: {
      type: Array,
      required: true
    },
    rows: {
      type: Array,
      required: true
    },
    rowKey: {
      type: String,
      required: true
    }
  },
  emits: ['handleButton'],
  methods: {
    handleButton(field, row) {
      const data = {
        field,
        row
      };
      this.$emitExtendable('handleButton', data);
    }
  }
};
</script>

<style lang="scss">
.profile-card:first-child {
  margin: auto;
}

.q-table--grid .q-table__bottom {
  border-top: solid 1px rgba(0,0,0,0.12);
}
</style>
