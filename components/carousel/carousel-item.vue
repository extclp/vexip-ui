<script setup lang="ts">
import { computed, inject, onBeforeUnmount, reactive } from 'vue'

import { useNameHelper } from '@vexip-ui/config'
import { CAROUSEL_STATE } from './symbol'

import type { ItemState } from './symbol'

defineOptions({ name: 'CarouselItem' })

defineSlots<{
  default: (params: { active: boolean }) => any
}>()

const carouselState = inject(CAROUSEL_STATE, null)

const nh = useNameHelper('carousel')
const state: ItemState = reactive({
  label: -1,
  width: 0,
  height: 0,
  offset: 0
})

const active = computed(() => {
  return carouselState?.isItemActive(state.label) ?? false
})
const className = computed(() => {
  return {
    [nh.be('item')]: true,
    [nh.bem('item', 'active')]: active.value
  }
})
const style = computed(() => {
  const { width, height, offset } = state

  return {
    width: width ? `${width}px` : undefined,
    height: height ? `${height}px` : undefined,
    transform: offset ? `translate${carouselState?.vertical ? 'Y' : 'X'}(${offset}px)` : undefined
  }
})

if (carouselState) {
  carouselState.increaseItem(state)

  onBeforeUnmount(() => {
    carouselState.decreaseItem(state)
  })
}

function handleClick() {
  carouselState?.handleSelect(state.label)
}
</script>

<template>
  <div
    :class="className"
    role="listitem"
    :style="style"
    @click="handleClick"
  >
    <slot :active="active"></slot>
  </div>
</template>
