<template>
  <view :class="classes" @click="handleClickStep">
    <view class="nut-step-head">
      <view class="nut-step-line"></view>
      <view class="nut-step-icon" :class="[!dot ? 'is-icon' : '']">
        <view class="nut-step-icon-inner">
          <slot name="icon">
            <template v-if="!dot">
              <view class="nut-step-inner">{{ index + 1 }}</view>
            </template>
          </slot>
        </view>
      </view>
    </view>
    <view class="nut-step-main">
      <view class="nut-step-title">
        <slot name="title">
          <span>{{ title }}</span>
        </slot>
      </view>
      <view v-if="content || $slots.content" class="nut-step-content">
        <slot name="content">
          <span v-html="content"></span>
        </slot>
      </view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useParent } from '@/packages/utils'
import { STEPS_KEY } from '../steps/types'

defineOptions({
  name: 'NutStep'
})

export type StepProps = Partial<{
  title: string
  content: string
}>

withDefaults(defineProps<StepProps>(), {
  title: '',
  content: ''
})

const { index, parent } = useParent(STEPS_KEY)

const status = computed(() => {
  const activeIndex = index.value + 1
  if (activeIndex < +parent.props.current) return 'finish'
  return activeIndex === +parent.props.current ? 'process' : 'wait'
})

const dot = computed(() => parent.props.progressDot)

const classes = computed(() => {
  const prefixCls = 'nut-step'
  return {
    [prefixCls]: true,
    [`${prefixCls}-${status.value}`]: true
  }
})

const handleClickStep = () => {
  parent.onEmit(index.value + 1)
}
</script>
