<template>
  <n-space vertical style="padding: 20px;">
    <n-switch v-model:value="showTimeline">
      <template #checked>
        Hide Timeline
      </template>
      <template #unchecked>
        Show Timeline
      </template>
    </n-switch>
    <n-collapse-transition :show="showTimeline">
      <n-timeline>
        <n-timeline-item
          v-for="message in person.messageList" 
          :key="message.title"
          :title="message.title"
          :content="renderMessageContent(message)"
          :color="message.color ? message.color : '#63e2b7'"
          :time="message.timestamp"
        >
          <template #icon>
            <n-icon size="20">
              <component :is="renderIcon(message.icon)" />
            </n-icon>
          </template>
        </n-timeline-item>
      </n-timeline>
    </n-collapse-transition>
  </n-space>
</template>

<script>
import { defineComponent, h } from 'vue'
import { 
  NCollapseTransition,
  NIcon,
  NSpace,
  NSwitch,
  NTimeline,
  NTimelineItem,
} from 'naive-ui'
import { AccessTimeOutlined as TimeIcon } from '@vicons/material'
import TGameMessage from '@/components/TGameMessage.vue'
import useMessage from '@/composables/useMessage'
import { Person } from '@/entities/Person'

export default defineComponent({
  components: {
    NCollapseTransition,
    NIcon,
    NSpace,
    NSwitch,
    NTimeline,
    NTimelineItem,
  },
  props: {
    person: Person,
  },
  setup() {
    const { showTimeline } = useMessage();

    //todo - the content slot is only supposed to allow a string, so using this spits out warnings
    const renderMessageContent = (message) => {
      return h(TGameMessage, { messageList: [message] });
    }

    return {
      renderIcon: (icon) => icon? h(icon) : h(TimeIcon),
      renderMessageContent,
      showTimeline,
    }
  },
})
</script>