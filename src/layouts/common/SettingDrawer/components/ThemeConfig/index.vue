<template>
  <n-divider title-placement="center">主题配置</n-divider>
  <n-space vertical>
    <div ref="copyRef" :data-clipboard-text="dataClipboardText">
      <n-button type="primary" :block="true">拷贝当前配置</n-button>
    </div>
    <n-button type="warning" :block="true" @click="handleResetConfig">重置当前配置</n-button>
  </n-space>
</template>

<script setup lang="ts">
import { ref, watch, onMounted, onUnmounted } from 'vue';
import { NDivider, NSpace, NButton } from 'naive-ui';
import Clipboard from 'clipboard';
import { useThemeStore } from '@/store';

const theme = useThemeStore();

const copyRef = ref<HTMLElement>();
const dataClipboardText = ref(getClipboardText());

function getClipboardText() {
  return JSON.stringify(theme.$state);
}

function handleResetConfig() {
  theme.resetThemeStore();
  window.$message?.success('已重置配置，请重新拷贝！');
}

function clipboardEventListener() {
  if (!copyRef.value) return;
  const copy = new Clipboard(copyRef.value);
  copy.on('success', () => {
    window.$dialog?.success({
      title: '操作成功',
      content: '复制成功,请替换 src/settings/theme.json的内容！',
      positiveText: '确定'
    });
  });
}

const stopHandle = watch(
  () => theme.$state,
  () => {
    dataClipboardText.value = getClipboardText();
  },
  { deep: true }
);

onMounted(() => {
  clipboardEventListener();
});
onUnmounted(() => {
  stopHandle();
});
</script>
<style scoped></style>
