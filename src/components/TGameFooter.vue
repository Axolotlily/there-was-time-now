<template>
  <n-layout-footer bordered>
    <n-space justify="end" style="padding: 5px;">
      <n-tooltip placement="top" trigger="hover">
        <template #trigger>
          <n-button strong circle @click="about()">
            <template #icon>
              <n-icon><about-icon /></n-icon>
            </template>
          </n-button>
        </template>
        <span>About</span>
      </n-tooltip>
      <n-tooltip placement="top" trigger="hover">
        <template #trigger>
          <n-button strong circle @click="version()">
            <template #icon>
              <n-icon><version-icon /></n-icon>
            </template>
          </n-button>
        </template>
        <span>Version History</span>
      </n-tooltip>
      <n-tooltip placement="top" trigger="hover">
        <template #trigger>
          <n-button strong circle @click="switchTheme">
            <template #icon>
              <n-icon>
                <light-mode-icon v-if="!lightMode" />
                <dark-mode-icon v-if="lightMode" />
              </n-icon>
            </template>
          </n-button>
        </template>
        <span>{{ otherThemeName() }}</span>
      </n-tooltip>
      <n-tooltip placement="top" trigger="hover">
        <template #trigger>
          <n-button strong circle @click="pauseTime()">
            <template #icon>
              <n-icon><pause-icon /></n-icon>
            </template>
          </n-button>
        </template>
        <span>Pause</span>
      </n-tooltip>
      <n-tooltip placement="top" trigger="hover">
        <template #trigger>
          <n-button strong circle @click="importGame()">
            <template #icon>
              <n-icon><import-icon /></n-icon>
            </template>
          </n-button>
        </template>
        <span>Import</span>
      </n-tooltip>
      <n-tooltip placement="top" trigger="hover">
        <template #trigger>
          <n-button strong circle @click="exportGame()">
            <template #icon>
              <n-icon><export-icon /></n-icon>
            </template>
          </n-button>
        </template>
        <span>Export</span>
      </n-tooltip>
      <n-tooltip placement="top" trigger="hover">
        <template #trigger>
          <n-button strong circle @click="restart()">
            <template #icon>
              <n-icon><restart-icon /></n-icon>
            </template>
          </n-button>
        </template>
        <span>Restart</span>
      </n-tooltip>
    </n-space>
  </n-layout-footer>
</template>

<script>
import { defineComponent, h, ref } from 'vue'

import { 
  NButton,
  NIcon,
  NLayoutFooter,
  NSpace,
  NTooltip,
  useDialog,
  useMessage,
} from 'naive-ui'

import {
  QuestionOutlined as AboutIcon,
  PauseOutlined as PauseIcon,
} from '@vicons/antd'

import {
  IosGitBranch as VersionIcon,
} from '@vicons/ionicons4'

import { 
  DarkModeOutlined as DarkModeIcon,
  FileUploadOutlined as ImportIcon,
  LightModeOutlined as LightModeIcon,
  RestartAltOutlined as RestartIcon,
  SaveOutlined as ExportIcon,
} from '@vicons/material'

import TGameAbout from '@/components/settings/TGameAbout.vue'
import TGameExport from '@/components/settings/TGameExport.vue'
import TGameImport from '@/components/settings/TGameImport.vue'
import TGameVersion from '@/components/settings/TGameVersion.vue'
import useGameMessage from '@/composables/useMessage'
import usePause from '@/composables/usePause'
import useSaveLoad from '@/composables/useSaveLoad'
import useTheme from '@/composables/useTheme'
export default defineComponent({
  components: {
    AboutIcon,
    DarkModeIcon,
    ExportIcon,
    ImportIcon,
    LightModeIcon,
    NButton,
    NIcon,
    NLayoutFooter,
    NSpace,
    NTooltip,
    PauseIcon,
    RestartIcon,

    VersionIcon,
  },
  setup() {
    const dialog = useDialog();
    const { sendInitialMessage } = useGameMessage();
    const message = useMessage();
    const { pause, unpause } = usePause();
    const { clearGameState, exportGameState, importGameState } = useSaveLoad();
    const { lightMode, switchTheme } = useTheme();

    const about = () => {
      pause();
      dialog.create({
        title: 'About',
        content: () => h(TGameAbout),
        icon: () => h(AboutIcon),
        positiveText: 'Neat',
        onPositiveClick: unpause,
        onMaskClick: unpause,
        onClose: unpause,
      });
    }

    const version = () => {
      pause();
      dialog.create({
        title: 'Version History',
        content: () => h(TGameVersion),
        icon: () => h(VersionIcon),
        positiveText: 'Good to Know',
        onPositiveClick: unpause,
        onMaskClick: unpause,
        onClose: unpause,
      });
    }

    const pauseTime = () => {
      pause();
      dialog.create({
        title: 'Paused',
        content: 'There was time now... to go to the bathroom.',
        icon: () => h(PauseIcon),
        positiveText: 'Back to it!',
        onPositiveClick: unpause,
        onMaskClick: unpause,
        onClose: unpause,
      });
    }

    const importGame = () => {
      pause();
      const importError = ref(false);
      const importDialog = dialog.create({
        title: 'Import Game',
        content: () => h(TGameImport, { 
          importError,
          onCancelImport: () => { 
            unpause(); 
            importDialog.destroy(); 
          },
          onImportString: $event => {
            const successfulImport = importGameState($event.value);
            if(successfulImport) {
              importError.value = false;
              importDialog.destroy();
              message.info("Successfully imported game.")
            } else {
              importError.value = true;
            }
          },
        }),
        icon: () => h(ImportIcon),
        onMaskClick: unpause,
        onClose: unpause,
      });
    }

    const exportGame = () => {
      pause();
      const exportMessage = ref('');
      const exportDialog = dialog.create({
        title: 'Export Game',
        content: () => h(TGameExport, {
          exportMessage,
          exportString: exportGameState(),
          onCopyToClipboard: $event => {
            console.log($event);
            navigator.clipboard.writeText($event)
              .then(() => exportMessage.value = 'Copy successful!')
              .catch(() => exportMessage.value = 'Error copying to clipboard.')
          },
          onCloseExport: () => {
            unpause();
            exportDialog.destroy();
          }
        }),
        icon: () => h(ExportIcon),
        onMaskClick: unpause,
        onClose: unpause,
      });
    }

    const otherThemeName = () => {
      if (lightMode.value) {
        return 'Dark Mode';
      } else {
        return 'Light Mode';
      }
    }

    const restart = () => {
      pause();
      dialog.create({
        title: 'Restart Game?',
        content: 'You will lose all of your progress!',
        icon: () => h(RestartIcon),
        positiveText: 'I\'m going back to the start',
        negativeText: 'Oops, never mind',
        onPositiveClick: () => {
          clearGameState().then(sendInitialMessage);
        },
        onNegativeClick: unpause,
        onMaskClick: unpause,
        onClose: unpause,
      });
    }

    return {
      about,
      exportGame,
      importGame,
      lightMode,
      otherThemeName,
      pauseTime,
      restart,
      switchTheme,
      version,
    }
  },
})
</script>