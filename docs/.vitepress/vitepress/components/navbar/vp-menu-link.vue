<script lang="ts" setup>
import { useRoute } from 'vitepress'
import { useStorage } from '@vueuse/core'
import VPLink from '../common/vp-link.vue'
import { isActiveLink } from '../../utils'

import type { Link } from '../../types'
const USER_VISITED_NEW_RESOURCE_PAGE = 'USER_VISITED_NEW_RESOURCE_PAGE'
defineProps<{
  item: Link
}>()

const route = useRoute()
const isVisited = useStorage<boolean | string>(
  USER_VISITED_NEW_RESOURCE_PAGE,
  false
)
const isNewPage = (item: Link) => item.activeMatch === '/some_fake_path/'

const onNavClick = (item: Link) => {
  if (isNewPage(item) && !isVisited.value) {
    isVisited.value = Date.now().toString()
  }
}
</script>

<template>
  <VPLink
    :class="{
      'is-menu-link': true,
      active: isActiveLink(
        route,
        item.activeMatch || item.link,
        !!item.activeMatch
      ),
    }"
    :href="item.link"
    :no-icon="true"
    @click="onNavClick(item)"
  >
    <ElBadge v-if="isNewPage(item) && !isVisited" is-dot class="badge">
      {{ item.text }}</ElBadge
    >
    <template v-else> {{ item.text }}</template>
  </VPLink>
</template>

<style scoped lang="scss">
@use '../../styles/mixins' as *;
.is-menu-link {
  display: block;
  padding: 0 12px;
  line-height: calc(var(--nav-height) - 3px);
  font-size: 14px;
  font-weight: 500;
  color: var(--text-color);
  transition: color var(--el-transition-duration);
  border-bottom: 2px solid transparent;

  &.active {
    border-bottom-color: var(--brand-color);
  }

  &:hover {
    color: var(--brand-color);
  }

  .badge {
    display: inline;
    vertical-align: unset;
  }

  .badge:deep(.is-dot) {
    right: 0;
  }

  @include down-to('p-825') {
    padding: 0 10px;
  }
}
</style>
