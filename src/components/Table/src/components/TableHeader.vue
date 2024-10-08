<template>
  <div style="width: 100%">
    <div v-if="$slots.headerTop" style="margin: 5px">
      <slot name="headerTop"></slot>
    </div>
    <div class="flex items-center">
      <slot name="tableTitle" v-if="$slots.tableTitle"></slot>
      <TableTitle
        :helpMessage="props.titleHelpMessage"
        :title="props.title"
        v-if="!$slots.tableTitle && props.title"
      />
      <div :class="`${prefixCls}__toolbar`">
        <slot name="toolbar"></slot>
        <Divider type="vertical" v-if="$slots.toolbar && props.showTableSetting" />
        <TableSetting
          :setting="props.tableSetting"
          v-if="props.showTableSetting"
          @columns-change="handleColumnChange"
        />
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
  import type { ColumnChangeParam } from '../types/table';
  import type { PropType } from 'vue';
  import { Divider } from 'ant-design-vue';
  import TableTitle from './TableTitle.vue';
  import { useDesign } from '@/hooks/web/useDesign';
  import TableSettingComponent from './settings/index.vue';

  const TableSetting = TableSettingComponent;

  const props = defineProps({
    title: {
      type: [Function, String] as PropType<string | ((data) => string)>,
    },
    tableSetting: {
      type: Object as PropType<TableSetting>,
    },
    showTableSetting: {
      type: Boolean,
    },
    titleHelpMessage: {
      type: [String, Array] as PropType<string | string[]>,
      default: '',
    },
  });

  const emit = defineEmits(['columns-change']);

  const { prefixCls } = useDesign('basic-table-header');
  function handleColumnChange(data: ColumnChangeParam[]) {
    emit('columns-change', data);
  }
</script>
<style lang="less">
  @prefix-cls: ~'@{namespace}-basic-table-header';

  .@{prefix-cls} {
    &__toolbar {
      display: flex;
      flex: 1;
      align-items: center;
      justify-content: flex-end;

      > * {
        margin-right: 8px;
      }
    }
  }
</style>
