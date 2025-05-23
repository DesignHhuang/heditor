<template>
  <t-dropdown
    :attach="`${container} .umo-page-container`"
    placement="bottom-right"
    overlay-class-name="umo-block-menu-dropdown"
    :max-height="320"
    trigger="click"
    :destroy-on-close="false"
    :popup-props="popupProps"
  >
    <menus-button
      class="umo-block-menu-button"
      :menu-active="menuActive"
      ico="block-add"
      hide-text
    />
    <t-dropdown-menu>
      <t-dropdown-item class="umo-block-menu-group-name" disabled>
        {{ t('blockMenu.insert') }}
      </t-dropdown-item>
      <t-dropdown-item>
        <menus-button
          ico="table"
          :text="t('table.insert.text')"
          :tooltip="false"
          @menu-click="editor?.chain().focus().insertTable().run()"
        />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('image')">
        <menus-toolbar-insert-image :huge="false" :tooltip="false" />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('video')">
        <menus-toolbar-insert-video :huge="false" :tooltip="false" />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('file')">
        <menus-toolbar-insert-file :huge="false" :tooltip="false" />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('code-block')">
        <menus-toolbar-insert-code-block
          :huge="false"
          shortcut-text="Ctrl+Alt+C"
          :tooltip="false"
        />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('callout')">
        <menus-toolbar-insert-callout :huge="false" :tooltip="false" />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('hr')">
        <menus-button
          ico="hr"
          :text="t('insert.hr.text')"
          :tooltip="false"
          @menu-click="editor?.chain().focus().setHr({ type: 'signle' }).run()"
        />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('qrcode')">
        <menus-toolbar-tools-qrcode :huge="false" :tooltip="false" />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('barcode')">
        <menus-toolbar-tools-barcode :huge="false" :tooltip="false" />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('signature')">
        <menus-toolbar-tools-signature :huge="false" :tooltip="false" />
      </t-dropdown-item>
      <t-dropdown-item v-if="!disableItem('seal')">
        <menus-toolbar-tools-seal :huge="false" :tooltip="false" />
      </t-dropdown-item>
    </t-dropdown-menu>
  </t-dropdown>
</template>

<script setup lang="ts">
import type { Template } from '@/types'

const emits = defineEmits<{
  dropdownVisible: (visible: boolean) => void
}>()

const container = inject('container')
const editor = inject('editor')
const blockMenu = inject('blockMenu')
const assistant = inject('assistant')
const options = inject('options')

let menuActive = $ref(false)
const popupProps = {
  onVisibleChange(visible: boolean) {
    editor.value.commands.focus()
    blockMenu.value = visible
    menuActive = visible
    emits('dropdownVisible', visible)
  },
}

const disableItem = (name: string) => {
  return options.value.toolbar?.disableMenuItems.includes(name)
}

const openAssistant = () => {
  assistant.value = true
  editor.value?.commands.selectParentNode()
  editor.value?.commands.focus()
  const { from, to } = editor.value?.state.selection ?? {}
  editor.value?.commands.setTextSelection({ from: from ?? 0, to: to ?? 0 })
}

const setTemplate = ({ content }: Template) => {
  if (!content || !editor.value) {
    return
  }
  editor.value.commands.insertContent(content)
}
</script>

<style lang="less"></style>
