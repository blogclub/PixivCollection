<template>
  <div class="h-[60px]">
    <div
      class="fixed top-0 left-0 w-full h-[60px] z-10 bg-white dark:bg-[#202020] transition-[transform,background] shadow-md"
      :class="{
        'translate-y-[-70px]': (!showNav || imageViewer.show) && !showSidebar,
      }"
    >
      <div
        class="absolute top-0 left-0 flex"
        :class="{
          'w-full': filterConfig.search.enable,
        }"
      >
        <button
          class="w-[60px] h-[60px] hover:bg-gray-400/20"
          @click="showSidebar = !showSidebar"
        >
          <IconMenu class="w-7 h-7 mx-auto" />
        </button>
        <button
          class="w-[60px] h-[60px] hover:bg-gray-400/20 hidden sm:block"
          @click="store.toggleSearch"
        >
          <IconSearch v-if="!filterConfig.search.enable" class="w-6 h-6 mx-auto" />
          <IconClose v-else class="w-6 h-6 mx-auto" />
        </button>
        <div v-show="filterConfig.search.enable" class="flex-1 mr-[60px] sm:mr-0">
          <input
            class="w-full h-[60px] outline-none px-4 box-border border-x border-gray-400/50 bg-transparent"
            type="text"
            placeholder="图片id/图片标题/作者id/作者昵称/标签"
            :value="filterConfig.search.value"
            @input="handleSearchInput"
          >
        </div>
      </div>
      <button
        class="w-[60px] h-[60px] hover:bg-gray-400/20 absolute right-0 block sm:hidden"
        @click="store.toggleSearch"
      >
        <IconSearch v-if="!filterConfig.search.enable" class="w-6 h-6 mx-auto" />
        <IconClose v-else class="w-6 h-6 mx-auto" />
      </button>
      <div
        v-show="!filterConfig.search.enable"
        class="text-lg h-[60px] leading-[60px] select-none text-center mx-[60px]"
        @dblclick="navToTop"
      >
        OriLightの<span class="text-[#0398fa] px-1">Pixiv</span>收藏夹
      </div>
      <div class="absolute top-0 right-0 hidden lg:flex">
        <button
          class="w-[60px] h-[60px] hover:bg-gray-400/20"
          @click="openGithub"
        >
          <IconGithub class="w-6 h-6 mx-auto" />
        </button>
        <button
          class="w-[60px] h-[60px] hover:bg-gray-400/20"
          @click="store.toggleColorScheme"
        >
          <IconSun v-if="preferColorScheme === 'light'" class="w-6 h-6 mx-auto" />
          <IconMoon v-if="preferColorScheme === 'dark'" class="w-5 h-5 mx-auto" />
          <IconAuto v-if="preferColorScheme === 'auto'" class="w-5 h-5 mx-auto" />
        </button>
        <button
          class="w-[60px] h-[60px] hover:bg-gray-400/20"
          @click="store.toggleFullscreen"
        >
          <IconShrink v-if="isFullscreen" class="w-5 h-5 mx-auto" />
          <IconExpand v-else class="w-5 h-5 mx-auto" />
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useDebounceFn } from '@vueuse/core'
import { useStore } from '@/store'
import { LINK_GITHUB, NAVBAR_HIDE_DISTANCE } from '@/config'

const store = useStore()
const { preferColorScheme, showSidebar, showNav, imageViewer, isFullscreen, filterConfig } = toRefs(store)

const updateSearchStr = useDebounceFn((value) => {
  store.updateSeatchValue(value)
}, 300)

let oldY = 0

onMounted(() => {
  window.addEventListener('scroll', () => {
    const newY = document.documentElement.scrollTop
    if (newY > oldY && newY > NAVBAR_HIDE_DISTANCE)
      showNav.value = false
    else if (newY < oldY)
      showNav.value = true
    oldY = newY
  })
})

function navToTop() {
  document.documentElement.scrollIntoView({ behavior: 'smooth', block: 'start' })
}

function openGithub() {
  window.open(LINK_GITHUB, '_blank')
}

function handleSearchInput(e: Event) {
  const value = (e.target as HTMLInputElement).value
  updateSearchStr(value)
}
</script>
