<template>
  <div :class="[prefixCls, { fullscreen }]">
    <Upload
      name="file"
      multiple
      @change="handleChange"
      :action="uploadUrl"
      :headers="headerData"
      :showUploadList="false"
      accept=".jpg,.jpeg,.gif,.png,.webp"
    >
      <a-button
        type="primary"
        preIcon="material-symbols:image-sharp"
        class="mt-1"
        :iconSize="14"
        v-bind="{ ...getButtonProps }"
      >
        {{ t('component.upload.imgUpload') }}
      </a-button>
    </Upload>
  </div>
</template>
<script lang="ts" setup>
  import { computed } from 'vue';

  import { Upload } from 'ant-design-vue';
  import { useDesign } from '@/hooks/web/useDesign';
  import { useGlobSetting } from '@/hooks/setting';
  import { useI18n } from '@/hooks/web/useI18n';
  import { useUserStore } from '@/store/modules/user';

  const props = defineProps({
    fullscreen: {
      type: Boolean,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
  });

  const emit = defineEmits(['uploading', 'done', 'error']);

  let uploading = false;

  const { uploadUrl } = useGlobSetting();
  const userStore = useUserStore();
  const { t } = useI18n();
  const { prefixCls } = useDesign('tinymce-img-upload');

  const getButtonProps = computed(() => {
    const { disabled } = props;
    return {
      disabled,
    };
  });

  const headerData = { Authorization: userStore.getToken };

  function handleChange(info: Recordable) {
    const file = info.file;
    const status = file?.status;
    const url = file?.response?.data?.url;
    const name = file?.name;

    if (status === 'uploading') {
      if (!uploading) {
        emit('uploading', name);
        uploading = true;
      }
    } else if (status === 'done') {
      emit('done', name, url);
      uploading = false;
    } else if (status === 'error') {
      emit('error');
      uploading = false;
    }
  }
</script>
<style lang="less" scoped>
  @prefix-cls: ~'@{namespace}-tinymce-img-upload';

  .@{prefix-cls} {
    position: absolute;
    z-index: 20;
    top: 4px;
    right: 10px;

    &.fullscreen {
      position: fixed;
      z-index: 10000;
    }
  }
</style>
