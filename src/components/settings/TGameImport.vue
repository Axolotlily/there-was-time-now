<template>
  <n-space vertical>
    <span><em>This will overwrite your progress!</em></span>
    <span v-if="importError.value" :style="{color: '#FF4136'}">Error importing game.</span>
    <n-input 
      v-model:value="imp"
      :autosize="{minRows: 10, maxRows: 10}"
      placeholder="Paste your export here"
      type="textarea"
    />
    <n-space justify="end">
      <n-button @click="cancelImport">
        Cancel
      </n-button>
      <n-button type="warning" @click="importString">
        Import
      </n-button>
    </n-space>
  </n-space>
</template>

<script>
import { defineComponent, ref } from 'vue'
import { NButton, NInput, NSpace } from 'naive-ui'

export default defineComponent({
  components: {
    NButton,
    NInput,
    NSpace,
  },
  props: {
    importError: {
      type: Object,
      required: true
    }
  },
  emits: ['cancel-import', 'import-string'],
  setup(_, { emit }) {
    const imp = ref('');

    const importString = () => {
      emit('import-string', imp);
    }

    const cancelImport = () => {
      emit('cancel-import');
    }

    return {
      cancelImport,
      imp,
      importString,
    }
  },
})
</script>
